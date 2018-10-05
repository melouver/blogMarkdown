---
title: offerDay4
date: 2018-09-13 18:01:54
tags: offer
---

# 1
```
数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。
```
```C++
bool moreThanHalf(vector<int> v, int& res) {
    int curNum = v[0], cnt = 1;
    
    for (int i = 1; i < v.size(); i++) {
        if (v[i] == curNum) {
            cnt++;
        } else {
            cnt--;
            if (!cnt) {
                curNum = v[i];
                cnt = 1;
            }
        }
    }
    res = curNum;
    cnt = 0;
    for (int i = 0; i < v.size(); i++) {
        if (v[i] == curNum) cnt++;
    }
    return cnt > v.size()/2;
}
```





