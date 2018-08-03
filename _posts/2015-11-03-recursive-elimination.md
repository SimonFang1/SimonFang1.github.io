---
layout: post
title: 递归的消除——令人又爱又恨的递归
date: 2015-11-03
categories: blog
tags: [数据结构, 递归, 性能优化]
description:
---

递归对于分析问题比较有优势，但是基于递归的实现效率可能不高。另外，因为函数栈大小的限制，递归的层次也有限制。
本文给出一种通用的消除递归的步骤，这样可以在分析阶段采用递归思想得到算法，而在实现阶段可采用非递归算法。

请读者注意，对于结构化的高级语言，由于不能使用goto，本文的方法优化的非递归算法与原递归算法运行效率可能并没有太大的改进，
且会对代码的可读质量造成严重破坏。是否进行递归消除应视情况而定。



## 代码的分块

分块位于每个递归调用处，调用语句执行后即发生跳转。
给每个分块分配一个标签名。

考虑最简单的情况，递归函数无返回值，函数体都是顺序语句，则代码块可如下划分：

```c++
void foo(T args, ...) {
  -------------------
  | /* label 0 */   |
  | ...             |
  | foo(args, ...)  |
  -------------------
  | /* label 1 */   |
  | ...             |
  | foo(args, ..)   |
  -------------------
  | .               |
  | .               |
  | .               |
  -------------------
  | /* label N */   |
  | ...             |
  | foo(args, ..)   |
  -------------------
}
```

## 定义栈帧

栈帧代表了代码执行的上下文，
将递归版本代码体中用到的局部值类型变量定义为栈帧的成员变量，
另外还需要定义一个返回标签（地址）成员变量。

```c++
struct StackFrame {
  T1 arg1;
  T2 arg2;
  ...
  int retLabel;
};
```
## while循环体和switch结构
在 while 循环之前声明一个栈(stack)和跳转地址(label)，并初始化。
```c++
#include <stack>

using std::stack;

void foo(args,...) {
  stack<StackFrame> framestack;
  framestack.push(StackFrame(args,...));
  int label = 0;
  while (true) {
    args = framestack.top();
    switch (label){
      case 0: {

        break;
      }
      case 1: {
        
        break;
      }
      ...
      case N: {
        
        break;
      }
    }
  }
}
```
## 填充 case 代码体

函数的调用是基于栈，每次调用都涉及如下操作：

* 调用开始时：将返回地址和函数参数入栈。
* 调用结束时：出栈并将返回到入栈时的返回地址。

将递归版本的代码做如下变换：
* 函数调用使用：framestack.push(StackFrame(args,..., retLabel)); label = 0; break;。
* return 语句变为：label = framstack.top().retLabel; framstack.pop(); break;

## 二叉树练习
[二叉树中和为某一值的路径](https://www.nowcoder.com/practice/b736e784e3e34731af99065031301bca?tpId=13&tqId=11177&tPage=2&rp=2&ru=%2Fta%2Fcoding-interviews&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking)

### 题目描述

输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。

### 参考代码
```c++
#include <iostream>

#include <stack>

#include <vector>

using namespace std;

struct TreeNode {
  int val;
  struct TreeNode *left;
  struct TreeNode *right;
  TreeNode(int x) :
      val(x), left(NULL), right(NULL) {
  }
};

class Solution {    
  inline bool isLeaf(TreeNode *node) {
    return node->left == nullptr && node->right == nullptr;
  }
  vector<vector<int> > res;
  vector<int> path;
  void Traverse(TreeNode* root, int n) {
    // label: 0
    if (!root) return;
    path.push_back(root->val);
    if (isLeaf(root) && root->val == n) {
      res.push_back(path);
    }
    if (root->left) Traverse(root->left, n - root->val);
    // label: 1
    if (root->right) Traverse(root->right, n - root->val);
    // label: 2
    path.pop_back();
  }

  struct StackFrame {
    TreeNode *root;
    int num;
    int retLabel;
    StackFrame(TreeNode *r, int n, int l = 0) {
      root = r;
      num = n;
      retLabel = l;
    }
  };

  void Traverse_ex(TreeNode *root, int n) {
    stack<StackFrame> framestack;
    framestack.push(StackFrame(root, n));
    int label = 0;
    while (!framestack.empty()) {
      auto params = framestack.top();
      root = params.root;
      n = params.num;
      switch (label) {
        case 0:
          if (!root) {
            label = params.retLabel;
            framestack.pop();
            break;
          }
          path.push_back(root->val);
          if (isLeaf(root) && root->val == n) {
            res.push_back(path);
          }
          if (root->left) {
            framestack.push(StackFrame(root->left, n-root->val, 1));
            label = 0;
            break;
          }
        case 1:
          if (root->right) {
            framestack.push(StackFrame(root->right, n-root->val, 2));
            label = 0;
            break;
          }
        case 2:
          path.pop_back();
          label = params.retLabel;
          framestack.pop();
          break;
      }
    }
  }
public:
  vector<vector<int> > FindPath(TreeNode* root, int expectNumber) {
    res.clear();
    path.clear();
    Traverse_ex(root, expectNumber);
    return res;
  }
};

TreeNode * NewSimpleTree() {
  TreeNode *p = new TreeNode(10);
  p->left = new TreeNode(5);
  p->right = new TreeNode(12);
  TreeNode *q = p->left;
  q->left = new TreeNode(4);
  q->right = new TreeNode(7);
  return p;
}

int main() {
  TreeNode *root = NewSimpleTree();
  Solution s;
  auto list = s.FindPath(root, 22);
  // use gdb to print list
  cout << list.size() << endl;
  // delete root is unnecessary
  return 0;
}
```
