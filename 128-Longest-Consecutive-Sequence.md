---
title: 128. Longest Consecutive Sequence
date: 2019-02-24 18:42:10
tags: lc
---

相当于一些数字分布在数轴上多个连续区域，求这些连续区域中的最长值。
我们用hash table来实现常数时间查找及删除，然后从该数字向左右延伸。

```
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int>s(nums.begin(),nums.end());
        int res=0;
        for(int n:nums){
            if(s.find(n)==s.end())continue;
            int pre=n-1,next=n+1;
            s.erase(n);
            while(s.find(pre)!=s.end())s.erase(pre--);
            while(s.find(next)!=s.end())s.erase(next++);
            res=max(res,next-pre-1);
        }
        return res;
    }
};
```


