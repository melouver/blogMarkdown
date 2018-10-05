---
title: lc-112. Path Sum II
date: 2018-09-07 21:43:55
tags: lc
---

这次是返回所有路径，只需要维护一下根节点到当前节点的路径即可(push-> recursive -> pop)


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
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        vector<vector<int>> v;
        vector<int> cur;
        helper(root, v, cur, sum);
        return v;
    }
    
    void helper(TreeNode* root, vector<vector<int>> &res, vector<int> &cur, int sum)  {
        if (!root) return;
        if (root->val == sum && !root->left && !root->right) {
            cur.push_back(root->val);
            res.push_back(cur);
            cur.pop_back();
            return;
        }
        cur.push_back(root->val);
        helper(root->left, res, cur, sum-root->val);
        helper(root->right, res, cur, sum-root->val);
        cur.pop_back();
        
    }
};
```
