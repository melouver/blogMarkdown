---
title: lc-222 Count Complete Tree Nodes
date: 2018-09-07 17:50:41
tags: lc
---
```
Given a complete binary tree, count the number of nodes.

Note:

Definition of a complete binary tree from Wikipedia:
In a complete binary tree every level, except possibly the last, is completely filled, and all nodes in the last level are as far left as possible. It can have between 1 and 2h nodes inclusive at the last level h.
```



注意这个树是complete tree，也就是说，除了最后一层，其它层都是满的，且最后一层的所有节点是从左到右连续的
我们逐步从根节点向下试探，考虑根节点的高度和右子树的高度，如果他们高度差1，则说明右子树在整个树的最后一层是有节点的，因此可知左子树是满的，
所以节点数加上1<<根节点树高，然后向右子树走。否则说明最后一层的最右节点存在于左子树中，因此加上1<<(根节点高度-1),因为这一次只知道右子树是满的，且高度少1，这里我们每次都加了子树及根节点，所以是2的幂。并往左子树走。

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
    int height(TreeNode* root) {
        int res = -1;
        while (root) {
            res++;
            root = root->left;
        }
        return res;
    }
    int countNodes(TreeNode* root) {
        int h = height(root);
        int res = 0;
        while (root) {
            if (height(root->right) == h-1) {
                res += 1<<h;
                root = root->right;
            } else {
                res += 1 << h-1;
                root = root->left;
            }
            h--;
        }
        return res;
    }
};


```
