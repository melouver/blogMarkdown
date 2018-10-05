---
title: offerDay1
date: 2018-09-08 21:35:03
tags: offer
---
# 1

```
在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
```

我们从左下角或者右上角开始查找，每次比较target和当前值，可以唯一确定正确的行走方向
```C++
// 2d vector -> int** || int*

bool find(vector<vector<int>> v, int target) {
   if (v.size() == 0 || v[0].size() == 0) return false;
   int x = v.size()-1, y = 0;
   
   while (x < v.size() && y >= 0) {
       if (v[x][y] > target) {
           x--;
       } else if (v[x][y] < target) {
           y++:
       } else {
           return true;
       }
   }
   return false;
}
```

# 2
```
请实现一个函数，将一个字符串中的空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。
```
先扩容，然后从后往前写入新的字符，这样能避免不必要的移动
```C++
void repl(char *&str,int length) {
    if (!str || length == 0) return;
    
    int cnt = 0;
    int i =length-1;
    for (int i = 0; i < length; cnt += (str[i] == ' '));
    str = (char*)realloc(str, length += 2*cnt);
    for (int j = length-1; i >= 0; i--) {
        if (str[i] == ' ') {
            str[j--] = '0';
            str[j--] = '2';
            str[j--] = '%';
        } else {
            str[j--] = str[i];
        }
    }
}
```

# 3
```
输入一个链表，从尾到头打印链表每个节点的值。
```

两种做法，显式栈和递归

显式栈
```C++
vector<int> printListFromTailToHead(ListNode* head) {
    stack<ListNode*> s;
    
    while (head) {
        s.push(head);
        head = head->next;
    }
    vector<int> res;
    while (!s.empty()) {
        res.push_back(s.top()->val);
        s.pop();
    }
    return res;
}


```

递归
```C++
vector<int> printListFromTailToHead(ListNode* head) {
    vector<int> res;
    helper(root, res);
    return res;
}

void helper(ListNode* root, vector<int>& res) {
    if (!root) return;
    res.push_back(root->val);
    helper(root->next, res);
}

```


# 4
```
用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。
加上线程安全!
```

```C++
class Queue {
    stack<int> s1, s2;
    std::mutex mu;
    void push(int x) {
        mu.lock();
        s1.push(x);
        mu.unlock()
    }
    
    bool pop(int& res) {
        mu.lock()
        if (s2.empty()) {
            while (!s1.empty()) {
                s2.push(s1.top());
                s1.pop();
            }
        }
        if (s2.empty()) return false;
        int res = s2.top();s2.pop();
        mu.unlock()
        return true;
    }
};

```
follow up:两个队列实现一个栈
```C++
class Stack{
    queue<int> q1, q2;
    
    void push(int x) {
        queue<int> *pushq = q1.size() == 0? &q2 : &q1;
        pushq->push(x);
    }
    
    bool pop(init& res) {
        auto &popq = q1.size() == 0? q2 : q1;
        auto &other = q1.size() == 0 ?q1:q2;
        if (popq.size() == 0) return false;
        while (popq.size() != 1) {
            otherpush(popq.front());
            popq.pop();
        }
        res = popq.front(), popq.pop();
        return true;
    }


};
```
# 5
```
把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。

NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。
```
二分搜索变形题
```C++
int rotateMin(vector<int> rotate) {
    if (rotate.size() == 0)return 0;
    int l = 0, r = rotate.size()-1;
    int mid = l;
    while (rotate[l] >= rotate[r]) {
        if (r-l == 1) {
            mid = r;
            break;
        }
        mid = (l+r)/2;
        if (rotate[l] == rotate[r] && rotate[l] == rotate[mid]) {
            return helper(rotate, l, r);
        }
        if (rotate[mid] >= rotate[l]) {
            l = mid;
        } else if (rotate[mid] <= rotate[r]) {
            r = mid;
        }
    }
    return rotate[mid];
}

int helper(vector<int> rotate, int l, int r) {
    int res = rotate[l];
    for (int i = l+1; i <= r; res = min(res, rotate[i++])) {
    }
    return res;
}
```
# 6
```
大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项。
```
```C++
int fib(int n) {
    int a = 1, b = 1;
    if (n < 0) return -1;
    if (n <= 1) return 1;
    n -= 2;
    while (n--) {
        int tb = b;
        b = a+b;
        a = tb;
    }
    return a;
}

```








