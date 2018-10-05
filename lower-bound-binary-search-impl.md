---
title: upper & lower bound(binary search) impl
date: 2018-09-08 08:49:47
tags: 
---
面试快手的时候被问到了，当时我使用的是左右两个下标表示区间，写出来我觉得也没啥大问题，细节有问题而已，最后还是因为岗位实在不match，跪了，现在参考stl的实现重写一遍

```C++
int lb(int A[], int first, int last, int target) {
  int len = last-first;
  while (len) {
    int l2 = len/2;
    int m = first + l2;
    if (A[m] < target) {
      first = ++m;
      len -= l2+1;
    } else {
      len = l2;
    }
  }
  return first;
}
```

upper bound同理，只不过这次等号取值不一样。
```C++
int ub(int A[], int first, int last, int target) {
  int len = last - first;
  while (len) {
    int l2 = len/2;
    int m = first + l2;
    if (target < A[m]) {
      len = l2;
    } else {
      first = ++m;
      len -= l2+1;
    }
  }
  return first;
}
```



