---
title: lc-112. Path Sum
date: 2018-09-07 21:25:10
tags: lc
---

```
Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.

Note: A leaf is a node with no children.

```
如果当前节点为叶节点并且剩余和刚好等于val，则说明存在，否则返回左递归和右递归的或

```C++
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
    bool hasPathSum(TreeNode* root, int sum) {
        if (!root) return false;
        if (root->val == sum && !root->left && !root->right) {
            return true;
        }
        return hasPathSum(root->left, sum-root->val) || hasPathSum(root->right, sum-root->val);
        
    }
};

```










