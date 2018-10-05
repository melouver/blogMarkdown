---
title: offerDay3
date: 2018-09-10 09:19:32
tags: offer
---
# 1
```
输入一个链表，输出该链表中倒数第k个结点。
```

```C++
ListNode* findkth(ListNode* head, int k) {
  if (!head || k <= 0) return NULL;

  ListNode *fast = head, *slow = head;
  k--;
  while (k-- && fast) {
    fast = fast->next;
  }

  if (!fast) {
    return NULL;
  }

  while (fast->next) {
    fast = fast->next;
    slow = slow->next;
  }

  return slow;
}
```

# 2
```
调整数组顺序使奇数位于偶数前面
```
这其实是一个排序问题，我们可以用普通的方法，也可以修改排序比较函数
```C++

void oddBeforeEven(vector<int>& v) {
    int odd = 0, even = 0;
    while (odd < v.size() && v[odd]%2 == 1) {
        odd++;
    }
    for (int i = odd; i < v.size(); i++) {
        if (v[i]%2 == 1) {
           swap(v[odd], v[i]);
           odd++;
        }
    }
}

void oddBeforeEven(vector<int> &v) {
    vector<pair<int,int>> arr;
    for (int i = 0; i < v.size(); i++) {
        arr.emplace_back(v[i], i);
    }
    sort(arr.begin(), arr.end(), cmp);//或者用stable_sort
    for (int i = 0; i < arr.size(); i++) {
        v[i] = arr[i].first;
    }
}

bool cmp(const pair<int,int> &l, const pair<int,int> &r) {
    if (l.first%2 == 1 && r.first%2==0) {
        return true;
    }
    
    return l.second < r.second;
}

```

# 3
```
输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）。
```

递归写法
```C++
bool subStruture(TreeNode* A, TreeNode* B) {
    if (!A || !B) return false;
    return isSame(A, B) || subStruture(A->left, B) || subStruture(A->right, B);
}

bool isSame(TreeNode* A, TreeNode* B) {
    if (!B) return true;
    if (!A && B)return false;
    return A->val == B->val && isSame(A->left, B->left) && isSame(A->right, B->right);
}
```

# 4
```
操作给定的二叉树，将其变换为源二叉树的镜像。
```

```C++
void Mirror(TreeNode *pRoot) {
    if (!pRoot) return;
    swap(pRoot->left, pRoot->right);
    Mirror(pRoot->left);
    Mirror(pRoot->right);
}
```

# 5
```
输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。
```

```C++
TreeNode *convert(TreeNode* root, TreeNode*& pre) {
if (!root) return NULL;
auto ret = convert(root->left, pre);
if (!ret) {
ret = root;
}
if (pre) {
pre->right = root;
}
root->left = pre;
pre = root;
convert(root->right, pre);
return ret;
}
```

非递归写法，其实类似非递归中序遍历,记录好上一个输出的节点即可

```C++
TreeNode* convert(TreeNode* root) {
    if (!root) return NULL;
    stack<TreeNode*> s;
    TreeNode *p = root, *pre = NULL, *ret = NULL;
    while (p || !s.empty()) {
        if (p) {
            s.push(p);
            p = p->left;
        } else {
            p = s.top();
            s.pop();
            if (pre) {
               pre->right = p;
               ret = p;
            }
            p->left = pre;
            pre = p;
            p = p->right;
        }
    }
    return ret;
}
```

# 6
```
输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。

输入一个字符串,长度不超过9(可能有字符重复),字符只包括大小写字母。
```
使用next_permutation
```C++
vector<string> permu(string str) {
  vector<string> res;
  if (str.size() == 0) {
    return {};
  }
  set<string> se;
  sort(str.begin(), str.end());
  do {
    se.insert(str);
  } while (next_permutation(str.begin(), str.end()));
  for (auto it = se.begin(); it != se.end(); res.push_back(*it++));
  return res;
}
```

使用递归解法
```C++
struct Sol {
    int MapToIndex(char c) {
      return c-'a';
    }

    void dfs(vector<int> &cnt, vector<string> &ret, string str, int n) {
      if (str.size() == n) {
        ret.push_back(str);
        return;
      }
      for (int i = 0; i < cnt.size(); i++) {
        if (cnt[i]) {
          cnt[i]--;
          dfs(cnt, ret, str+string(1, i+'a'), n);
          cnt[i]++;
        }
      }
    }

    vector<string> Permutation(string str) {
      vector<string> ret;
      if (str.size() == 0)
        return {};

      vector<int> cnt(26, 0);
      for (auto k : str) {
        cnt[k-'a']++;
      }

      dfs(cnt, ret, "", str.size());
      return ret;
    }
};

void printV(vector<string> v) {
  for (auto k : v) {
    cout << k << endl;
  }
}
```
求子集
```C++
void subset(vector<int> v, vector<vector<int>> &ret) {
      for (int i = 0; i < (1<<v.size()); i++) {
        vector<int> tmp;
        for (int j = 0; j < v.size(); j++) {
          if (i & (1 << j)) {
            tmp.push_back(v[j]);
          }
        }
        ret.push_back(tmp);
      }
    }
```






















