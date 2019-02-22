---
title: LC295. Find Median from Data Stream
date: 2019-02-22 22:23:09
tags: lc
---

```
class MedianFinder {
public:
    priority_queue<long> small, large;
    /** initialize your data structure here. */
    MedianFinder() {
        
    }
    
    void addNum(int num) {
        small.push(num);
        large.push(-small.top());
        small.pop();
        if (small.size() < large.size()) {
            small.push(-large.top());
            large.pop();
        }
    }
    
    double findMedian() {
        return small.size() > large.size() ? 
            small.top():
        (small.top()-large.top())/2.0;
    }
};

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
 ```

用两个堆，并动态地让他们大小保持近似（相等或者small中多一个元素）。使用最大堆small记录较小的元素，使用最大对large记录元素的负值，这样他们的大小就翻转了，绝对值最小的数反而在上面。