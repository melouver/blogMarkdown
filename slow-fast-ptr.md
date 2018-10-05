---
title: slow&fast ptr
date: 2018-09-11 19:35:48
tags: lc
---

快慢指针是带环链表的经典解法，但同时也能用于解决如重复数字这个问题，先看环状链表求入口。
```C++
    ListNode *detectCycle(ListNode *head) {
        if (!head || !head->next) return NULL;
        
        ListNode *slow = head->next, *fast = head->next->next;
        
        while (fast && fast->next && slow != fast) {
            slow = slow->next;
            fast = fast->next->next;
        }
        
        if (!fast || !fast->next) return NULL;
        
        fast = head;
        
        while (fast != slow) {
            fast = fast->next;
            slow = slow->next;
        }
        
        return slow;
        
    }
```

注意几个点:
1. 初始slow和fast应该是一次步骤后的位置，否则第一个循环会有问题
2. 无环的条件，要及时退出。
3. 注意访问变量之前检查指针是否为空！！


再看这个题:
```
n+1个数，每个数属于[1,n]，这个数组一定有重复，找出重复的
```

```C++
int findDuplicate(vector<int>& nums) {
        if (nums.size() == 0) return -1;
        int slow = nums[0], fast = nums[nums[0]];
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[nums[fast]];
        }
        fast = 0;
        while (fast != slow) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return slow;
    }
```
类似上面，注意初始指针位置。这次一定有环，故不担心越界



