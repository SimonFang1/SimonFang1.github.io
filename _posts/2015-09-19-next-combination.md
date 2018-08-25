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
#include <vector>
#include <iostream>

using namespace std;

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
  uint i = k - 2;
  while (i > 0 && index[i] == index[i+1] - 1) --i;
  if (i == 0 && index[i] == index[i+1] - 1) return false;
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
  vector<int> v({13, 28, 50, 64, 79});
  vector<unsigned> vi({0,1,2});
  cout << "data: ";
  for (auto &x: v) {
    cout << x << " ";
  }
  cout << endl;
  do {
    for (auto &i: vi) {
      cout << "["<< i << "] " << v[i] << " ";
    }
    cout << endl;
  } while (next_combination(vi.begin(), 3, 5));
  return 0;
}
```
### output
```
data: 13 28 50 64 79 
[0] 13 [1] 28 [2] 50 
[0] 13 [1] 28 [3] 64 
[0] 13 [1] 28 [4] 79 
[0] 13 [2] 50 [3] 64 
[0] 13 [2] 50 [4] 79 
[0] 13 [3] 64 [4] 79 
[1] 28 [2] 50 [3] 64 
[1] 28 [2] 50 [4] 79 
[1] 28 [3] 64 [4] 79 
[2] 50 [3] 64 [4] 79 
```
