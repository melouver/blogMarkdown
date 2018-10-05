---
title: lc-236. Lowest Common Ancestor of a Binary Tree
date: 2018-09-07 17:56:40
tags: lc
---

```
Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.

According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow a node to be a descendant of itself).”
```
常规面试题，求最低公共祖先
这里我们用路径交集的方法
首先求出根节点到两个节点的路径，放进stack里面(之前放在了vector里面，其实也ok)，然后不断pop两个stack，直到它们顶部不同，那么上一个出栈的节点就是公共祖先

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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        stack<TreeNode*> r1, r2;
        helper(root, p, r1);
        helper(root, q, r2);
        
        TreeNode* last = NULL;
        while (!r1.empty() && !r2.empty() && r1.top() == r2.top()) {
            last = r1.top();
            r1.pop();
            r2.pop();
        }
        return last;
    }
     
    bool helper(TreeNode* root, TreeNode* target, stack<TreeNode*> &res) {
        if (!root) return false;
        if (root == target) {
            res.push(root);
            return true;
        }
        bool lr = helper(root->left, target, res);
        bool rr = helper(root->right, target, res);
        if (lr || rr) {
            res.push(root);
            return true;
        }
        return false;
    }
    
    
};
```
递归写法
```C++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
       if (!root || p == root || q == root) return root;
       auto l = lowestCommonAncestor(root->left, p, q);
       auto r = lowestCommonAncestor(root->right, p, q);
       return !l ? r : !r ? l : root;
    }
  
    
    
};






```
