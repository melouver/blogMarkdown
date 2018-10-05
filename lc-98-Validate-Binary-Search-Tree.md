---
title: lc-98  Validate Binary Search Tree
date: 2018-09-07 17:18:12
tags: lc
---
```
Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

The left subtree of a node contains only nodes with keys less than the node's key.
The right subtree of a node contains only nodes with keys greater than the node's key.
Both the left and right subtrees must also be binary search trees.
```


判断一个树是否是BST依据是中序遍历是否按序，也等价于对于每个节点，其左子树的所有节点都小于它，右子树的所有节点都大于它。

使用一个指针保存上一次遍历的节点的地址，指针初始为空，表示根节点之前无节点，并且我们使用引用，这样避免使用二级指针，接着按正常中序遍历即可,注意规避根节点的情况(last为null)


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
    bool isValidBST(TreeNode* root) {
        int *last = NULL;
        return helper(root, last);
        
    }
    
    bool helper(TreeNode* root, int* & last) {
        if (!root) return true;
        bool lr = helper(root->left, last);
        if (!lr) return false;
        if (last) {
            if (*last >= root->val) return false;
        }
        last = &(root->val);
        return helper(root->right, last);
        
        
    }
};

```
