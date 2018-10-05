---
title: offerDay2
date: 2018-09-09 22:16:38
tags: offer
---

# 1
```
一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法。
```

```C++
int jump(int n) {
    // dp[i] = dp[i-1] + dp[i-2]
    if (n < 0) return -1;
    if (n <= 2) return n;
    int l = 1, r = 2, ret;
    for (int i = 2; i <= n; i++) {
        ret = r;
        r = r+l;
        l = ret;
    }
    return ret;
}
```

# 2

```
一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。
```

```C++
// 1:1
// 2:2
// 3:f(1)+f(2)+1 = 4
// 4:f(1)+f(2)+f(3)+1 = 8
// f(n) = 2^(n-1)
return 1<<n-1;

```

# 3
```
我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？
```

```C++
int overlap(int n) {
    //dp[i] = dp[i-1] + dp[i-2]

}



```

# 4
```
给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。
```

```C++

double power(double base, int exp) {
    bool f = exp < 0;
    if ((exp = abs(exp)) == 0)
        return 1.0;
    
    double ret = power(base, exp/2);
    ret = exp % 2? ret * ret * base: ret * ret;
    return f ? 1.0 / ret : ret;
}
```

# 5
```
输入一个链表，反转链表后，输出链表的所有元素。
```

注意头节点的更新（返回指针或者传入引用)

```C++
// recursive
ListNode* reverseList(ListNode* head) {
   if (!head || !head->next) {
      return head;
   }
   auto pre = reverseList(head->next);
   head->next->next = head;
   head->next = NULL;
   return pre;
}

// non recursive
ListNode* reverseList(ListNode* head) {
    if (!head || !head->next) {
        return head;
    }
    
    ListNode* last = NULL;
    ListNode* cur = head;
    
    while (cur) {
        ListNode* n = cur->next;
        cur->next = last;
        last = cur;
        cur = n;
    }
    
    return last;
}
```
# 6
```
输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。
```

递归写法：
```C++
ListNode* mergeList(ListNode* p, ListNode* q) {
  if (!p) return q;
  if (!q) return p;

  ListNode*& target = p->val < q->val ? p : q;
  ListNode*& other = p->val < q->val ? q : p;
  target->next = mergeList(target->next, other);
  return target;
}
```

迭代写法
```C++

ListNode* mergeList(ListNode* p, ListNode* q) {
  if (!p) return q;
  if (!q) return p;
  ListNode* res = new ListNode(0), *tmp = res;
  while (p && q) {
    ListNode*& target = p->val < q->val ? p : q;
    res->next = target;
    target = target->next;
    res = res->next;
  }

  res->next = p ? p : q;
  res = tmp->next;
  delete tmp;
  return res;
}


```







