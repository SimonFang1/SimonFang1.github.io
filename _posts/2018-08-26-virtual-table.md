---
layout: post
title: 自顶向下逆向理解C++虚表
date:  2018-08-26
categories: blog
tags: [C++]
description: 
---

C++的多态作为面试必考题,必然会问及实现方式,也就是虚表.为了更好地理解虚表,利用C/C++灵活访问内存的特性,可以显式地通过虚表调用虚函数,并观察虚表内虚函数的分布顺序.

## Q1

__虚表何时创建,不同的实例共享虚表吗?__

```c++
#include <iostream>

using namespace std;


class A {
public:
    static int s_a;
    int m_a;
    virtual void foo () {
        cout << "A::foo()" << endl;
    }
    virtual void bar () {
        cout << "A::bar()" << endl;
    }
    void baz() {
        cout << "A::baz()" << endl;
    }
};

int A::s_a = 1;

void test1() {
    A a1, a2;
    void ** ppa1 = (void **)&a1;
    void ** ppa2 = (void **)&a2;

    void* sys_stack_ptr;

    if (sizeof(sys_stack_ptr) == 8) {
        __asm__("movq %%rbp, %0\n\t"
                :"=r"(sys_stack_ptr)        /* output */
                :         /* input */
                :        /* clobbered register */
                );
    } else {
        __asm__("movl %%ebp, %0\n\t"
                :"=r"(sys_stack_ptr)        /* output */
                :         /* input */
                :        /* clobbered register */
                );
    }
    cout << "&A::s_a: " << &A::s_a 
         << ", located in data segment."<< endl;
    cout << "%rbp = " << sys_stack_ptr 
         << ", located in stack" << endl << endl;

    cout << "a1:" << endl;
    cout << "stack address: " << ppa1 << endl;
    cout << "vt address: " << *ppa1 << endl;
    cout << "m_a address: " << &a1.m_a << endl << endl;

    cout << "a2:" << endl;
    cout << "stack address: " << ppa2 << endl;
    cout << "vt address: " << *ppa2 << endl;
    cout << "m_a address: " << &a2.m_a << endl << endl;

}

int main() {
    test1();
    return 0;
}
```

```
&A::s_a: 0x563a819d6010, located in data segment.
%rbp = 0x7ffd11e3d7d0, located in stack

a1:
stack address: 0x7ffd11e3d7a0
vt address: 0x563a819d5d68
m_a address: 0x7ffd11e3d7a8

a2:
stack address: 0x7ffd11e3d7b0
vt address: 0x563a819d5d68
m_a address: 0x7ffd11e3d7b8
```

可见,虚表在编译阶段生成,位于数据段,每个类仅有一份,构造函数为每个实例初始化一个虚表指针.

## Q2

__虚函数和成员函数的调用有何不同?__

```c++

// Definition of A

void test2() {
    A *pa = new A;

    void (*func)(void *);

    // pa->foo

    void **ppa = (void **)pa;
    func = reinterpret_cast<void (*)(void *)>(*(void **)(*ppa));
    cout << "func address: " << (void *)func << endl;
    func(pa);

    // pa->bar

    func = reinterpret_cast<void (*)(void *)>(*((void **)(*ppa)+1));
    cout << "func address: " << (void *)func << endl;
    func(pa);

    // pa->baz
    func = reinterpret_cast<void (*)(void *)>((void *)&A::baz);
    cout << "func address: " << (void *)func << endl;
    func(pa);

    cout << endl;

    delete pa;
}

int main() {
    test2();
    return 0;
}

```


```
func address: 0x5626dcaa4184
A::foo()
func address: 0x5626dcaa41bc
A::bar()
func address: 0x5626dcaa41f4
A::baz()
```

不考虑链接,编译器依次编译函数,各函数的入口地址是相对固定的.
调用成员函数,保存好现场后,直接跳转至其入口地址,
而虚函数的入口地址编译时不能确定,需要在运行时查表.
成员函数或普通函数编译后,得到的是相对代码段的一个偏移地址(程序链接并成功装载后就是一个固定地址);
而虚函数编译后,得到的是相对虚表地址的一个偏移,以及如何通过读取虚表得到入口地址的相关指令.


## Q3

__子类可能含有多个虚表吗?__

```c++
class B: public A {
public:
    int m_b;
    void foo () {
        cout << "B::foo()" << endl;
    }
    void bar () {
        cout << "B::bar()" << endl;
    }
    virtual void foobar() {
        cout << "B::foobar()" << endl;
    }
    virtual void baz() {
        cout << "B::baz()" << endl;
    }
};

class C: public A {
public:
    int m_b;
    virtual void foo () {
        cout << "C::foo()" << endl;
    }
    virtual void bar () {
        cout << "C::bar()" << endl;
    }
    virtual void foobar() {
        cout << "C::foobar()" << endl;
    }
};

void test3() {
    A *pa[3];
    pa[0] = new A;
    pa[1] = new B;
    pa[2] = new C;

    void (*func)(void*);

    void ***pppa = (void ***)pa[0];
    func = reinterpret_cast<void (*)(void*)>((*pppa)[0]);
    cout << "func address: " << (void *)func << endl;
    func(pa[0]);
    func = reinterpret_cast<void (*)(void*)>((*pppa)[1]);
    cout << "func address: " << (void *)func << endl;
    func(pa[0]);

    void ***pppb = (void ***)pa[1];
    func = reinterpret_cast<void (*)(void*)>((*pppb)[0]);
    cout << "func address: " << (void *)func << endl;
    func(pa[1]);
    func = reinterpret_cast<void (*)(void*)>((*pppb)[1]);
    cout << "func address: " << (void *)func << endl;
    func(pa[1]);
    func = reinterpret_cast<void (*)(void*)>((*pppb)[2]);
    cout << "func address: " << (void *)func << endl;
    func(pa[1]);
    
    // Runtime Error
    // func = reinterpret_cast<void (*)(void*)>(*((void **)(*ppb)+3));
    // cout << "func address: " << (void *)func << endl;
    // func(pa[1]);

    pa[1]->baz();

    void ***pppc = (void ***)pa[2];
    func = reinterpret_cast<void (*)(void*)>((*pppc)[0]);
    cout << "func address: " << (void *)func << endl;
    func(pa[2]);
    func = reinterpret_cast<void (*)(void*)>((*pppc)[1]);
    cout << "func address: " << (void *)func << endl;
    func(pa[2]);
    func = reinterpret_cast<void (*)(void*)>((*pppc)[2]);
    cout << "func address: " << (void *)func << endl;
    func(pa[2]);


    cout << endl;

    delete pa[0];
    delete pa[1];
    delete pa[2];
}

int main() {
    test3();
    return 0;
}
```

```
func address: 0x55af0fd3f4e8
A::foo()
func address: 0x55af0fd3f520
A::bar()
func address: 0x55af0fd3f590
B::foo()
func address: 0x55af0fd3f5c8
B::bar()
func address: 0x55af0fd3f600
B::foobar()
A::baz()
func address: 0x55af0fd3f638
C::foo()
func address: 0x55af0fd3f670
C::bar()
func address: 0x55af0fd3f6a8
C::foobar()
```

虚表只有一个,虚表指针(如果有的话)是第一个成员变量.同名函数,如果基类不是虚函数,就没有多态功能,基类函数被子类隐藏,即使子类的同名函数增加了`virtual`,也和没加`virtual`效果一样,虚表不会增加新的函数地址;如果基类是虚函数,那么无论子类函数加不加`virtual`都是虚函数.

## Q4 - Q6

__构造函数可以是虚函数吗?__
__虚表指针在何时被初始化?(思考构造顺序)__
__为什么要显式地把析构函数声明为虚函数?__


```c++
class D {
    public:
    D() {
        void ***ppp = (void ***)this;
        cout << "this: " << this << endl;
        cout << "virtual table: " << *ppp << endl;
        cout << "construct D" << endl;
        void (*func)(void *);
        for (int i = 0; i < 4; ++i) {
            func = reinterpret_cast<void (*)(void *)>((*ppp)[i]);
            cout << "virtual table[" << i << "]: " << (void *)func << endl;
            // func(this);
        }
    }
    virtual void foo() {
        cout << "D::foo()" << endl;
    }
    virtual ~D() {
        cout << "~D" << endl;
    }
};

class E: public D {
    public:
    E() {
        void ***ppp = (void ***)this;
        cout << "this: " << this << endl;
        cout << "virtual table: " << *ppp << endl;
        cout << "construct E" << endl;
        void (*func)(void *);
        for (int i = 0; i < 4; ++i) {
            func = reinterpret_cast<void (*)(void *)>((*ppp)[i]);
            cout << "virtual table[" << i << "]: " << (void *)func << endl;
            // func(this);
        }
    }
    ~E() {
        cout << "~E" << endl;
    }
};

void test4() {
    E e;
}


int main() {
    test4();
    return 0;
}
```

```
this: 0x7fff4f4ea0b0
virtual table: 0x55e43b99ec60
construct D
virtual table[0]: 0x55e43b79dac8
virtual table[1]: 0x55e43b79db00
virtual table[2]: 0x55e43b79db46
virtual table[3]: 0
this: 0x7fff4f4ea0b0
virtual table: 0x55e43b99ec38
construct E
virtual table[0]: 0x55e43b79dac8
virtual table[1]: 0x55e43b79dcf0
virtual table[2]: 0x55e43b79dd42
virtual table[3]: 0
~E
~D
```

声明一个实例`e`,先后调用基类和子类的构造函数,可以看到虚表指针被改写,基类和子类的虚表内容不同.
调用构造函数,涉及到一系列的构造过程,虚表指针在运行时才被确定.实现上构造函数和虚表指针不可相互依赖;逻辑上具体的对象对应具体的构造函数,无需多态.

基类的构造过程：
1. 分配内存 (step a)
2. 初始化列表之前赋值虚表指针 (step c)
3. 列表初始化 (step d)
4. 执行构造函数体 (step e)

子类的构造过程：
1. 分配内存 (step a)
2. 基类构造过程（按照无继承来） (step b)
3. 初始化子类虚表指针 (step c)
4. 子类列表初始化 (step d)
5. 执行子类构造函数体 (step e)

列表初始化可以调用虚函数, 因此 step c 在 step d 之前, 虚表指针得以成为第一个"成员变量". 
用户定义的构造函数体,可自由地使用成员变量和虚函数,因此 step e 在 step d 之后.

多态一般通过基类指针管理,所以如果统一通过基类指针销毁对象,那么析构函数就应该是虚函数.
C++注重效率,如果隐式地帮助程序员给析构函数加上`virtual`,会导致运行效率下降.程序员应该清楚,对象应该如何正确地被析构,在代码编写难度和运行效率之间做trade off.

值得注意的是,把析构函数声明成虚函数后,虚表中多了两个函数.这是因为除了用户自定义的成员变量外,还需要删除C++ Runtime支持这个对象的数据结构,这一步不可能由用户完成,
参考https://www.zhihu.com/question/29257977/answer/606882770 

