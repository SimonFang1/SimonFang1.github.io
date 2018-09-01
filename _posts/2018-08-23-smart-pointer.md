---
layout: post
title: C++11智能指针的简单对比
date:  2018-08-23
categories: blog
tags: [C++11]
description: 
---

## 为什么使用智能指针
C++继承自C语言，在管理堆上的内存方面，赋予了程序员很大的灵活性。但繁琐内存管理的问题经常容易出错，稍不小心就会有内存泄露或段错误等问题。在C++中动态管理内存往往遵循申请内存和释放内存成对的原则，但是由于异常处理机制可能会增加函数的出口，因此程序员在异常发生后忘记释放内存的情况经常发生，导致内存泄漏。

在C++中，栈空间的对象是有生命周期的，在销毁的时候会执行它的析构函数。智能指针是可以自动管理堆上内存的特殊对象，它在自身析构函数中定义了对托管指针的处理，可谓智能。由于提供了指针操作接口(operator->,operator*)，实例表现得像指针一样，因此得名智能指针。

## ~~[auto_ptr](http://www.cplusplus.com/reference/memory/auto_ptr/)~~ [deprecatied since c++11]
auto_ptr被认为是设计不完善的智能指针，自c++11开始被废弃。auto_ptr负责维护一个对象指针，在自身析构的时候一定会通过delete方式释放非空指针。因此auto_ptr不能管理对象数组指针。为了避免重复释放，在auto_ptr实例之间赋值时，会转移托管指针的控制权，即在赋值完成后，托管指针变为nullptr。这一特性实际用处不大又不符合常规认知，因此在C++11中已经把auto_ptr给废除了，建议用unique_ptr代替。

虽然auto_ptr已经被废弃了，因为它的功能简单，但这里还是给出它的一个实现，以便更好地理解智能指针的概念。

```cpp

template<class T>
class auto_ptr {
public:
  explicit auto_ptr(T *p = 0): pointee(p) {}
  auto_ptr(auto_ptr<T>& rhs): pointee(rhs.release()) {}
  ~auto_ptr() { delete pointee; }
  auto_ptr<T>& operator=(auto_ptr<T>& rhs) {
      if (this != &rhs) reset(rhs.release());
      return *this;
  }
  T& operator*() const { return *pointee; }
  T* operator->() const { return pointee; }
  T* get() const { return pointee; }
  T* release() {
      T *oldPointee = pointee;
      pointee = 0;
      return oldPointee;
  }
  void reset(T *p = 0) {
      if (pointee != p) {
          delete pointee;
          pointee = p;
      }
  }
private:
  T *pointee;
};
```

C++11引入了3个新的智能指针，分别是unique_ptr, shared_ptr和 weak_ptr。这三个智能的模板参数可以是T或者T[].

## [unique_ptr](http://www.cplusplus.com/reference/memory/unique_ptr/)
unique_ptr特点有:
1. 非线程安全的
2. auto_ptr的替代品，因为它不提供copy Constructor和 Copy Assignable，也就是指针不能复制.
3. 只有一个智能指针对包含的实例对象有所有权，意思是只有一个智能指针在它生命周期结束后调用析构函数.
4. 它默认指针两种销毁对象的方式，一种是delete，另一种是delete[]，比auto_ptr多了数组方式的delete[].
5. 它没有引用计数和线程安全的方法，所以它的性能其实比shared_ptr要高，但是不适用无序的多线程环境.
6. 适用于自定义的deleter(也可以使用lambda)，即可以代理C的free，CloseHandle等.

unique_ptr是独占型的智能指针，它不允许其他的智能指针共享其内部的指针，不允许通过赋值将一个unique_ptr赋值给另一个unique_ptr，如下面错误用法：
```cpp
std::unique_ptr<T> myPtr(new T);
std::unique_ptr<T> myOtherPtr = myPtr; // error
```
但是unique_ptr允许通过函数返回给其他的unique_ptr，还可以通过[std::move](https://www.cnblogs.com/qicosmos/p/3369940.html)来转移到其他的unique_ptr，注意，这时它本身就不再拥有原来指针的所有权了。相比于auto_ptr而言，unique_ptr是显示的转移，而不是莫名其妙的报废，因为auto_ptr调用拷贝构造函数后，原来的对象就失效了。
```cpp
std::unique_ptr<T> myPtr(new T);
std::unique_ptr<T> myOtherPtr = std::move(myPtr); // ok
```

## [shared_ptr](http://www.cplusplus.com/reference/memory/shared_ptr/)
shared_ptr基于“引用计数”模型实现，多个shared_ptr可指向同一个动态对象，并维护了一个共享的引用计数器，记录了引用同一对象的shared_ptr实例的数量。当最后一个指向动态对象的shared_ptr销毁时，会自动销毁其所指对象。和unique_ptr一样，shared_ptr也支持自定义的Deleter以实现个性化的资源释放动作。
shared_ptr的创建有两种方式:
1. 使用函数make_shared（推荐）
2. 使用构造函数(可从原生指针、unique_ptr、另一个shared_ptr创建)

### shared_ptr的线程安全性
shared_ptr objects offer the same level of thread safety as built-in types.
shared_ptr对象提供与内置类型相同级别的线程安全性.
1. A shared_ptr instance can be "read" (accessed using only const operations) simultaneously by multiple threads.同一个shared_ptr对象可以被多线程同时读取。
2. Different shared_ptr instances can be "written to" (accessed using mutable operations such as operator= or reset) simultaneously by multiple threads (even when these instances are copies, and share the same reference count underneath.) 不同的shared_ptr对象可以被多线程同时修改（即使这些shared_ptr对象管理着同一个对象的指针）。
3. Any other simultaneous accesses result in undefined behavior. 任何其他并发访问的结果都是无定义的。也就是非原子函数会导致典型的进程同步问题。

### shared_ptr在循环引用的情况下会导致内存泄露

一般情况下，shared_ptr可以满足大部分的使用场景，但是对于循环引用的对象，在所有shared_ptr退出作用域后，引用计数不能减为0，导致管理的对象不能析构，发生内存泄露。

下面的代码定义了一个简单的链表类，使用shared_ptr作为智能指针来记录前后链表元素。通过对比可以看到在循环引用的情况下，share_ptr不能正常释放动态的元素。有时候仅仅使用shared_ptr是不够的，而weak_ptr正是为了解决循环引用而引入的。
```cpp
#include <iostream>

#include <memory>

using namespace std;

struct List {
  //T data;

  static int _count;
  List() {
    ++_count;
    cout << "create List, total: " << _count << endl;
  }
  ~List() {
    --_count;
    cout << "destroy List, total: " << _count << endl;
  }
  shared_ptr<List> prev, next;
};

int List::_count = 0;

int main() {
  { // A => B

    shared_ptr<List> head(new List);
    head->next = shared_ptr<List>(new List);
    cout << "shared_ptr.use_count() = " << head.use_count() << endl;
  }
  cout << endl;
  { // A <=> B

    shared_ptr<List> head(new List);
    head->next = shared_ptr<List>(new List);
    head->next->prev = head;
    cout << "shared_ptr.use_count() = " << head.use_count() << endl;
  }
  return 0;
}
```

运行结果
```bash
create List, total: 1
create List, total: 2
shared_ptr.use_count() = 1
destroy List, total: 1
destroy List, total: 0

create List, total: 1
create List, total: 2
shared_ptr.use_count() = 2
```

## [weak_ptr](http://www.cplusplus.com/reference/memory/weak_ptr/)

weak_ptr是一种不控制对象生命周期的智能指针，它指向一个 shared_ptr 管理的对象，并不增减该对象的引用计数器。能够进行对该对象的内存管理的是那个强引用的 shared_ptr，weak_ptr只是提供了对管理对象的一个访问手段，因此weak_ptr没有operator*和operator->方法，想要访问管理对象，必须调用lock()方法，临时构造一个shared_ptr。weak_ptr 设计的目的是为了配合 shared_ptr， 它只可以从一个 shared_ptr 或另一个 weak_ptr 对象构造。

### 用法
1. 有向树作为一种无环的图，可以全部使用shared_ptr来记录子节点。
2. 提供parent指针的有向树，parent指针用weak_ptr来记录，而child指针可以仍然用shared_ptr来记录。
3. 对于不可预料的场景，为了安全起见，可以先用shared_ptr存储在容器中，而对象内部的引用全部用weak_ptr来记录。
4. 其他情况应试具体的应用场景，选择合适的智能指针类型，甚至为了性能的最大化不使用智能指针。

下面用weak_ptr来改进上一节中的例程。
__注意：weak_ptr在调用lock()方法前必须先调用expire()方法检查是否过期，若shared_ptr已经销毁，则lock()方法返回的shared_ptr指向nullptr，这也是weak_ptr不重载operator*，operator->方法的原因。__

```cpp
#include <iostream>

#include <memory>

#include <vector>

using namespace std;

struct List {
  int data;
  static int _count;
  List(int d = 0) {
    data = d;
    ++_count;
    cout << "create List, total: " << _count << endl;
  }
  ~List() {
    --_count;
    cout << "destroy List, total: " << _count << endl;
  }
  weak_ptr<List> prev, next;
};

int List::_count = 0;

int main() {
  vector<int> data({0,10,20,30,40});
  vector<shared_ptr<List> > container;
  container.push_back(make_shared<List>());
  shared_ptr<List> first = container.back();
  weak_ptr<List> extra_head = first;
  for (auto &x: data) {
    container.push_back(make_shared<List>(x));
    shared_ptr<List> second(container.back());
    first->next = second;
    second->prev = first;
    first = second;
  }
  weak_ptr<List> head = extra_head.lock()->next;
  weak_ptr<List> cur = first;
  if (!cur.expired()) {
    {
      auto cur_ = cur.lock();
      cout << "cur->data = " << cur_->data << endl;
      cout << "cur.use_count() = " << cur.use_count() << endl;
      cout << "cur->prev.use_count() = " << cur_->prev.use_count() << endl;
    }
    first.reset();
    cout << "cur->data = " << cur.lock()->data << endl;
    cout << "cur.use_count() = " << cur.use_count() << endl;
    cout << "cur->next.use_count() = " << cur.lock()->prev.use_count() << endl;
  }
  return 0;
}

```

运行结果

```bash
create List, total: 1
create List, total: 2
create List, total: 3
create List, total: 4
create List, total: 5
create List, total: 6
cur->data = 40
cur.use_count() = 3
cur->prev.use_count() = 1
cur->data = 40
cur.use_count() = 1
cur->next.use_count() = 1
destroy List, total: 5
destroy List, total: 4
destroy List, total: 3
destroy List, total: 2
destroy List, total: 1
destroy List, total: 0
```
