---
title: lc-117. Populating Next Right Pointers in Each Node II
date: 2018-09-07 21:14:01
tags: lc
---
```
Given a binary tree

struct TreeLinkNode {
  TreeLinkNode *left;
  TreeLinkNode *right;
  TreeLinkNode *next;
}
Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.

Initially, all next pointers are set to NULL.

Note:

You may only use constant extra space.
Recursive approach is fine, implicit stack space does not count as extra space for this problem.
```
使用queue层序遍历
```C++
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        queue<TreeLinkNode*> q;
        if (!root) return;
        q.push(root);
        while (!q.empty()) {
            size_t sz = q.size();
            TreeLinkNode *last = NULL;
            for (size_t i = 0; i < sz; i++) {
                auto tmp = q.front(); q.pop();
                if (last) last->next = tmp;
                last = tmp;
                if (tmp->left) q.push(tmp->left);
                if (tmp->right) q.push(tmp->right);
            }
            last->next = NULL;
            
        }
    }
};
```
使用O(1)空间，我们维护两层关系，上一层已经被设置好所有next指针，下一层保存开头节点后结尾节点，然后按序尝试更新尾节点，最后到达下一层

```C++
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if (!root) return;
        for (auto head = root; head != NULL;) {
            auto nextHead = new TreeLinkNode(0), nextTail = nextHead;
            for (auto node = head; node != NULL; node = node->next) {
                if (node->left) {
                    nextTail->next = node->left;
                    nextTail = node->left;
                }
                if (node->right) {
                    nextTail->next = node->right;
                    nextTail = node->right;
                }
            }
            head = nextHead->next;
        }
        
        
    }
};
```


