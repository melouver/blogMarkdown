---
title: lc-450. Delete Node in a BST
date: 2018-09-07 22:28:56
tags: lc
---
```
Given a root node reference of a BST and a key, delete the node with the given key in the BST. Return the root node reference (possibly updated) of the BST.

Basically, the deletion can be divided into two stages:

Search for a node to remove.
If the node is found, delete the node.
Note: Time complexity should be O(height of tree).
```
使用前序遍历的结构，如果当前节点即是删除节点，则分两种情况:
1. 左右均非空，则把右子树的最小节点移动上来，并删除原节点
2. 返回非空的那个节点
否则根据BST性质来选择性在某个子树中删除.
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
    TreeNode* deleteNode(TreeNode* root, int key) {
        if (!root) return root;
        if (root->val == key) {
            if (root->left && root->right) {
                TreeNode *p = root->right;
                while (p->left) {
                    p = p->left;
                }
                root->val = p->val;
                root->right = deleteNode(root->right, p->val);
                return root;
            }
            return root->left ? root->left : root->right;
            
        }
        if (key < root->val) {
            root->left = deleteNode(root->left, key);
            return root;
        }
        root->right = deleteNode(root->right, key);
        return root;
    }
};
```


