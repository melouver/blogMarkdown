---
title: multiple LeetCode
date: 2019-02-21 09:45:22
tags: lc
---

LC283
给一组数，把其中的0全部移到末尾，其他元素保持顺序 

维持一个当前指针和非零数组的past by one指针
```
public void moveZeroes(int[] nums) {
        int i = 0;
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] != 0) {
                int tmp = nums[j];
                nums[j] = nums[i];
                nums[i] = tmp;
                i++;
            }
        }
    }
```
其实类似于quicksort的partition过程，这种题目还有关于荷兰旗的问题

***

LC349 求两个数组的交集

1.用两个hash set
```
public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set = new HashSet<>(), intersect = new HashSet<>();

        for (int i = 0; i < nums1.length; i++) {
            set.add(nums1[i]);
        }

        for (int i = 0; i < nums2.length; i++) {
            if (set.contains(nums2[i])) {
                intersect.add(nums2[i]);
            }
        }

        int[] res = new int[intersect.size()];
        int i = 0;
        for (Integer num : intersect) {
            res[i++] = num;
        }

        return res;
    }
```
2.分别排序后双指针
```
 public int[] intersection(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        Set<Integer> st = new HashSet<>();
        
        for (int i = 0, j = 0; i < nums1.length && j < nums2.length; i++, j++) {
            if (nums1[i] < nums2[j]) {
                i++;
            } else if (nums1[j] > nums2[j]) {
                j++;
            } else {
                st.add(nums1[i]);
                i++;
                j++;
            }
        }
        
        int[] res = new int[st.size()];
        int i = 0;
        for (Integer num : st) {
            res[i++] = num;
        }
        return res;
    }
```
3.排序后二分查找

***
二分查找不优雅的变种
```
int binarySearch(int[] num, int target) {
        int low = -1, high = num.length;

        while (low +1 != high) {
            int mid = low + (high - low) / 2;

            if (num[mid] <= target) {
                low = mid;
            } else if (num[mid] > target) {
                high = mid;
            }
        }

        if (high == num.length || num[low] != target) {
            return -1;
        }
        return high;
    }
```

***
236. Lowest Common Ancestor of a Binary Tree

在左右子树分别递归查找p，q节点，如果他们在同一边，则返回那一边的递归结果，否则，返回根节点。

```
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (!root || root == p || root == q) return root;
    TreeNode* left = lowestCommonAncestor(root->left, p, q);
    TreeNode* right = lowestCommonAncestor(root->right, p, q);
    return !left ? right : !right ? left : root;
}
```

***

117. Populating Next Right Pointers in Each Node II

这里要注意到一个特点：在处理第n层的next指针时，第n-1层的节点的next指针都已经被设置好，所以可以从左到右遍历第n-1层，即这里的level指针。

使用dummy节点，作为每一层的虚拟起始节点，在每一层结束时，dummy的next指针指向当前层的起始节点，我们利用完这个next信息后，需要把dummy变为白莲花，也就是next置空，这样它可以用于下一层了。
```
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
        for(TreeLinkNode *dummy=new TreeLinkNode(0);root!=NULL;root=dummy->next,dummy->next=NULL){
            for(TreeLinkNode*level=root,*last=dummy;level!=NULL;level=level->next){
                if(level->left){
                    last->next=level->left;
                    last=last->next;
                }
                if(level->right){
                    last->next=level->right;
                    last=last->next;
                }
            }
            
            
        }
    }
};
```
***
222. Count Complete Tree Nodes

不要在原函数上直接进行递归，会超时。
我们首先在对数时间内，通过最左路径找出两个字树的高度，然后根据子树高判断最后一层在哪个子树终止，并对对应的子树进行递归，同时得知另一子树的节点个数。

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
    int countNodes(TreeNode* root) {
        if(!root)return 0;
        
        int l =getHeight(root->left),r=getHeight(root->right);
        
        if(l==r){
            return (1<<(l+1))+countNodes(root->right);
        }else{
            return (1<<(r+1))+countNodes(root->left);
        }
    }
    
    int getHeight(TreeNode* root) {
        int res = -1;
        while (root) {
            res++;
            root=root->left;
        }
        return res;
    }
};
```
***
[LeetCode] Search in Rotated Sorted Array

旋转数组二分查找，注意先确定区间再调整
```
Int search(int[] A, int target) {
	int l = 0, r = A.length-1;

	while (l <= r) {
		int m = l + ((r-l)>>1);
		if (A[m] == target) return m;
		if (A[m] < A[r]) {
			if (A[m] < target && target <= A[r]) l = m+1;
			else r=m;
		} else {
			if (A[l] <= target && target <= A[r]) r = m;
			else l=m;
		}
	}
	return -1;
}
```
***
647. Palindromic Substrings
对于单字符和双字符，分别从每个起点开始扩展。

```
class Solution {
    public int countSubstrings(String s) {
        int res=0;
        for(int i=0;i<s.length();++i){
            res+=extendStr(s,i,i);
            res+=extendStr(s,i,i+1);
            
        }
        
        return res;
    }
    
    public int extendStr(String s,int i,int j){
        int c=0;
        while(i>=0&&j<s.length()&&s.charAt(i)==s.charAt(j)){
            i--;j++;c++;
        }
        return c;
    }
}
```
***
791. Custom Sort String
先统计T中字符个数，然后先填充S中有的字符，最后再末尾填充S没有的字符
```
public String customSortString(String S, String T) {
char[] res = T.toCharArray();

        int[] dict = new int[26];

        for (int i = 0; i != T.length(); i++) {
            dict[T.charAt(i)-'a']++;
        }
        int cur = 0;

        for (int i = 0; i != S.length(); i++) {
            for  (int j = 0; j != dict[S.charAt(i)-'a']; j++) {
                res[cur++] = S.charAt(i);
            }
            dict[S.charAt(i)-'a'] = 0;
        }

        for (int i = 0; i != dict.length; i++) {
            if (dict[i] != 0) {
                for (int j = 0; j != dict[i]; j++) {
                    res[cur++] = (char)(i+'a');
                }
            }
        }


        return new String(res);
}
```
***
890. Find and Replace Pattern
Char -> [0,25] map 到 [1,26]
M n记录该字符上一次出现的位置，如果不一致，说明两者模式不一样
```
    static public List<String> findAndReplacePattern(String[] words, String pattern) {
        List<String> res = new ArrayList<>();

        for (String w : words) {
            if (w.length() != pattern.length()) {
                continue;
            }

            int[] m = new int[26], n = new int[26];

            int i;
            for (i = 0; i != pattern.length(); ++i) {
                if (m[w.charAt(i)-'a'] == n[pattern.charAt(i)-'a']) {
                    m[w.charAt(i)-'a'] = n[pattern.charAt(i)-'a'] = i+1;
                } else {
                    break;
                }

            }

            if (i == pattern.length()) {
                res.add(w);
            }
        }
        return res;

    }
```
***
98. Validate Binary Search Tree

使用一个引用保存上一个节点
```
TreeNode max;

    public boolean isValidBST(TreeNode root){
        if(root == null){
            return true;
        }
        boolean left = isValidBST(root.left);
        boolean isBigger = true;
        if(max != null){
            isBigger = root.val > max.val;
        }
        max = root;
        return left && isBigger && isValidBST(root.right);
    }
```
注意return的写法，即同时满足左、中、右
***
[LeetCode] Permutation in String 字符串中的全排列


1. 滑动窗口
2. 哈希表+双指针
3. 两个哈希表



双哈希表:
```
public boolean checkInclusion(String s1, String s2) {
        int n1 = s1.length(), n2 = s2.length();
        if (n1 > n2) return false;
        int[] m1 = new int[128], m2 = new int[128];

        for (int i = 0; i != n1; i++) {
            m1[s1.charAt(i)]++;m2[s2.charAt(i)]++;
        }

        if (Arrays.equals(m1, m2)) return true;

        for (int i = n1; i < n2; ++i) {
            m2[s2.charAt(i)]++;
            m2[s2.charAt(i-n1)]--;
            if (Arrays.equals(m1, m2)) return true;
        }

        return false;

    }
```
一个哈希表+双指针:
```
public static boolean checkInclusion(String s1, String s2) {
        int n1 = s1.length(), n2 = s2.length(), left = 0;

        int[] m = new int[256];

        for (int i = 0; i != s1.length(); i++) {
            m[s1.charAt(i)]++;
        }

        for (int i = 0; i != s2.length(); ++i) {
            if (--m[s2.charAt(i)] < 0) {
                while (++m[s2.charAt(left++)] != 0)
                    ;
            } else if (i-left+1 == n1) return true;
        }

        return n1 == 0;

    }
```

***
二分求平分根
 ```
static double calRoot(int n) {
        double i = 1, j = n-1;

        while (i <= j) {
            double mid = i + ((j-i)/2.0);
            if (mid - i < 0.000001) {
                return j;
            }
            if (mid * mid < n) {
                i = mid;
            } else if (mid * mid > n) {
                j = mid;
            } else {
                return mid;
            }
        }

        return -1;
    }
```
***
88. Merge Sorted Array
```
public void merge(int[] nums1, int m, int[] nums2, int n) {
        int cur1 = m-1, cur2 = n-1;

        for (int i = nums1.length; cur2 >= 0; --i) {
            int num1 = cur1 < 0 ? Integer.MIN_VALUE : nums1[cur1];
            nums1[i] = num1 > nums2[cur2] ? nums1[cur1--] : nums2[cur2--];
        }
    }
```
从后往前填充，因为这样不会覆盖第一个数组中较大的元素

***
141. Linked List Cycle
```
public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) return false;
        
        ListNode slow = head, fast = head.next;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            
            if (slow == fast) return true;
        }
        
        return false;
    }
```
***

26. Remove Duplicates from Sorted Array

```
class Solution {
    public int removeDuplicates(int[] nums) {
         Integer last = null;
        int j = 0;
        for (int i = 0; i != nums.length; i++) {
            if (last == null || nums[i] != last) {
                last = nums[i];
                nums[j++] = nums[i];
            }
        }

        return j;
    }
}
```

简单的双指针，注意用last对象的空表示第一个元素的情况
***
27. Remove Element
```
class Solution {
    public int removeElement(int[] nums, int val) {
        int i, j;
        for (i = 0, j = 0; j != nums.length; j++) {
            if (nums[j] != val) {
                nums[i++] = nums[j];
            }
        }
        return i;
    }
}
```
简单的双指针
***
844. Backspace String Compare

public boolean backspaceCompare(String S, String T) {
        Stack<Character> st = new Stack<>(), st2 = new Stack<>();

        for (int i = 0; i != S.length(); i++) {
            if (S.charAt(i) != '#') {
                st.push(S.charAt(i));
            } else if (!st.empty()) {
                st.pop();
            }
        }

        for (int i = 0; i != T.length(); i++) {
            if (T.charAt(i) != '#') {
                st2.push(T.charAt(i));
            } else if (!st.empty()) {
                st2.pop();
            }
        }


        while (!st.empty() && !st2.empty()) {
            if (st.pop() != st2.pop()) {
                return false;
            }
        }

        return st.empty() && st2.empty();
    }

模拟并比较最终结果。

下面使用常数空间
```
public boolean backspaceCompare(String S, String T) {
        if (S == null || T == null) return S == T;

        int m = S.length(), n = T.length();
        int i = m-1, j = n-1;

        int cnt1 = 0, cnt2 = 0;

        while (i >= 0 || j >= 0) {
            while (i >= 0 && (cnt1 > 0 || S.charAt(i) == '#')) {
                if (S.charAt(i) == '#') cnt1++;
                else cnt1--;
                i--;
            }
            while (j >= 0 && (cnt2 > 0 || T.charAt(j) == '#')) {
                if (T.charAt(j) == '#') cnt2++;
                else cnt2--;
                j--;
            }
            if (i >= 0 && j >= 0 && S.charAt(i) == T.charAt(j)) {
                i--;j--;
            } else {
                return i == -1 && j == -1;
            }
        }

        return true;
    }
    ```
从后向前进行遍历，如果遇到#则记录下来，并且剔除掉之前的字符。

***
925. Long Pressed Name

如果typed中mismatch但是仍然是上一个match的字符，那么允许它重试
```
  public boolean isLongPressedName(String name, String typed) {
        char[] n = name.toCharArray();
        char[] t = typed.toCharArray();

        int j = 0;
        Character last = null;
        int i;
        for (i = 0; i != n.length && j != t.length; j++) {
            if (n[i] == t[j]) {
                last = n[i];
                i++;
                continue;
            }

            if (last == null || last != t[j]) {
                return false;
            }
        }

        return i == n.length;
    }
```

***
350. Intersection of Two Arrays II

这次每个数字出现的次数等于原来两个数组的出现次数之和

注意，用较小的数组去匹配较大的，这样遍历次数较少
```
public static int[] intersect(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        List<Integer> l = new ArrayList<>();

        for (int i = 0, j = 0; i < nums1.length && j < nums2.length;) {
            if (nums1[i] < nums2[j]) {
                i++;
            } else if (nums1[i] > nums2[j]) {
                j++;
            } else {
                l.add(nums1[i]);
                i++;
                j++;
            }
        }
        int[] res = new int[l.size()];
        int i = 0;
        for (Integer num : l) {
            res[i++] = num;
        }
        return res;
    }
```

***
逆序字符串，练习用java实现 344
```
 public String reverseString(String s) {
        int start = 0, end = s.length()-1;

        char[] arr = s.toCharArray();
        while (start < end) {
            char tmp = arr[start];
            arr[start] = arr[end];
            arr[end] = tmp;
            start++;
            end--;
        }

        return new String(arr);
    }


如果是用c呢？

int reverseString(char *str, int n) {
	if (n < 0) return -1;
	char *end = str+n-1;
	while (str < end) {
		swap(str++, end—);
	}
	return 0;
}

如果是c++呢

Bool reverseString(std::string &str) {
	for (int I = 0; I != str.size()/2; I++) {
		std::swap(str[I], str[str.size()-I-1]);
	}
	return true;
}

```


***

556 

找到下一个比n大的数

例如,n = 21,那么没有比它大的数，根本原因在于它是降序排列的。

例如,n=12,那么比它大的数就是21.

解法:
首先从右往左找到第一个非降序的数字，然后从右往左找第一个比该数字大的数，交换它们，并对后面这一段数进行排序即可。排序的原因在于有序的数是所有排列中最小的。
```
public  int nextGreaterElement(int n) {
        char[] number = (n + "").toCharArray();

        int i, j;

        for (i = number.length-1; i > 0; i--) {
            if (number[i-1] < number[i]) {
                break;
            }
        }

        if (i == 0) {
            return -1;
        }

        int x = number[i-1];

        for (j = number.length-1; j > i; j--) {
            if (number[j] > x) {
                break;
            }
        }

        char tmp = number[i-1];
        number[i-1] = number[j];
        number[j] = tmp;

        Arrays.sort(number, i, number.length);

        long val = Long.parseLong(new String(number));

        return (val <= Integer.MAX_VALUE ? (int)val : -1);
    }
    ```


***

4 

两个有序数组的中位数

1.总数组大小为m+n时，一个trick: 求 (第(m+n+1)/2大 + 第(m+n+2)/2大)/2 即是所求
2.中位数可转为求第k大的数
3.一次淘汰较小的k/2个数
```
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m = nums1.length, n = nums2.length, left = (m+n+1)/2, right = (m+n+2)/2;

        return (findKth(nums1, 0, nums2, 0, left) + findKth(nums1, 0, nums2, 0, right) )/2.0;
    }

    int findKth(int[] num1, int i, int[] num2, int j, int k) {
        if (i >= num1.length) return num2[j+k-1];
        if (j >= num2.length) return num1[i+k-1];

        if (k == 1) return Math.min(num1[i], num2[j]);

        int midVal1 = (i + k/2 - 1) < num1.length ? num1[i+k/2-1] : Integer.MAX_VALUE;
        int midVal2 = (j + k/2 - 1) < num2.length ? num2[j+k/2-1] : Integer.MAX_VALUE;

        if (midVal1 < midVal2) {
            return findKth(num1, i+k/2, num2, j, k-k/2);
        } else {
            return findKth(num1, i, num2, j+k/2, k-k/2);
        }
    }
```









