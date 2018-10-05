---
title: lc-112. Path Sum III
date: 2018-09-07 21:58:40
tags: lc
---

```
You are given a binary tree in which each node contains an integer value.

Find the number of paths that sum to a given value.

The path does not need to start or end at the root or a leaf, but it must go downwards (traveling only from parent nodes to child nodes).

The tree has no more than 1,000 nodes and the values are in the range -1,000,000 to 1,000,000
```

仍然保存根节点到当前的路径，然后每次从后向前尝试累加

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
    int pathSum(TreeNode* root, int sum) {
        ans = 0;
        vector<int> cur;
        helper(root, cur, sum);
        return ans;
    }
    int ans;
    void helper(TreeNode* root, vector<int> &cur, int sum) {
        if (!root) return;
        int su = root->val;
        if (su == sum) ans++;
        for (int i = 0; i < cur.size(); i++) {
            su += cur[cur.size()-i-1];
            if (su == sum) ans++;
        }
        cur.push_back(root->val);
        helper(root->left, cur, sum);
        helper(root->right, cur, sum);
        cur.pop_back();
    }
};
```
