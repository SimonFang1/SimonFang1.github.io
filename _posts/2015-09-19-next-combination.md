---
layout: post
title: next combination
date: 2015-09-19
categories: blog
tags: [algorithm, library]
description: 仿照next_permutation写的一个算法函数
---
## source code
```cpp
// generate next combination via index
// pick k number from S={0, 1, ..., n-1}
// 
// index is an accending array with size k, which elements belongs to S
// 
// index will be modified to the next combination and return true
// if index is the last combination, or prerequisition fails
// index remains and return false

template<class RandomAccessIterator, typename uint=unsigned>
bool next_combination(RandomAccessIterator index, uint k, uint n) {
  auto &last = index[k-1];
  if (last >= n) return false;
  if (last < n - 1) {
    ++last;
    return true;
  }
  unsigned i = k - 2;
  while (i > 0 && index[i] == index[i+1] - 1) --i;
  if (i < 0) return false;
  ++index[i];
  while (++i < k) {
    index[i] = index[i-1] + 1;
  }
  return true;
}
```
## test code
```cpp
#include "next_combination.h"

#include <iostream>

#include <vector>

using namespace std;

int main() {
  vector<int> v({0,1,2});
  do {
    for (auto &x: v) {
      cout << x << " ";
    }
    cout << endl;
  } while (next_combination(v.begin(), 3, 5));
  return 0;
}
```
### output
```
0 1 2 
0 1 3 
0 1 4 
0 1 5 
0 2 3 
0 2 4 
0 2 5 
0 3 4 
0 3 5 
0 4 5 
1 2 3 
1 2 4 
1 2 5 
1 3 4 
1 3 5 
1 4 5 
2 3 4 
2 3 5 
2 4 5 
3 4 5 
4 5 6 
```
