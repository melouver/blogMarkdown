---
title: 239. Sliding Window Maximum
date: 2019-02-24 18:08:52
tags: lc
---

本题求每个滑动窗口中的最大值，可以用堆实现，但单调队列会是更好的选择。我们维护一个单调减队列，并且队列中第i个元素中记录了大小在第i-1个到第i个的元素个数。这个信息能够归类一段数组，并以其最大值来代表这段数组，易于操作。
每次我们需要添加一个元素，删除一个元素，保证窗口大小恒定。

```
struct Helper{
    deque<pair<int,int>> mq;
    
    void pu(int val){
        int cnt=0;
        while(!mq.empty()&&mq.back().first<val){
            cnt+=mq.back().second+1;
            mq.pop_back();
        }
        mq.emplace_back(val,cnt);
    }
    
    int max(){
        return mq.front().first;
    }
    
    void pop(){
        if(mq.front().second>0){
            mq.front().second--;
            return;
        }
        
        mq.pop_front();
        
    }
    
};

class Solution {
public:
    
   
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        Helper hp;
        for(int i=0;i<k-1;i++){
            hp.pu(nums[i]);    
        }
        
        vector<int>result;
        
        for(int i=k-1;i<nums.size();i++){
            hp.pu(nums[i]);
            result.push_back(hp.max());
            hp.pop();
        }
        
        
        return result;
    }
};
```






