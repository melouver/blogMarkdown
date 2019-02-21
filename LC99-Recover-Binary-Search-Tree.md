---
title: LC99 Recover Binary Search Tree
date: 2019-02-21 10:15:01
tags: lc
---

```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    void recoverTree(TreeNode* root) {
        pre = first = sec = NULL;
        helper(root);
        
        swap(first->val, sec->val);
    }
    
    TreeNode* pre, *first, *sec;
    
    void helper(TreeNode*root) {
        if(!root) return;
        
        helper(root->left);
        
        if (pre) {
            if (!first && pre->val >= root->val) {
                first = pre;
            }

            if (first && pre->val>=root->val) {
                sec = root;
            }
        }
        pre = root;
        helper(root->right);
    }
};
```

想找出错位的两个节点，只需观察树的中序遍历结果。
如果把数值用线连起来，可以发现从左到右有一到两次下降的过程，次数取决于错位节点是否相邻。
中序遍历时，将第一次遇到的构成下降线的第一个节点记为first，将最后遇到的构成下降线的第二个节点记为sec，最后交换这俩。



