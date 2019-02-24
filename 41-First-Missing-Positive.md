---
title: 41. First Missing Positive
date: 2019-02-24 18:55:53
tags: lc
---
我们使用交换元素的思路，尽量把第i个数放置到i-1这个索引上，如果对方已经符合要求，则不交换，否则交换，直到某个数不能再动了。最后从头到尾检查第一个不在位的数。


```
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        for(int i=0;i<nums.size();i++){
            while(nums[i]!=i+1&&nums[i]>0&&nums[i]<nums.size()&&nums[i]!=nums[nums[i]-1]){
                swap(nums[i],nums[nums[i]-1]);
            }
        }
        
        for(int i=0;i<nums.size();i++){
            if(nums[i]!=i+1){
                return i+1;
            }
        }
        return nums.size()+1;
    }
};
```