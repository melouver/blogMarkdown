---
title: 求解满足某些条件的substring的通用模板
date: 2019-02-23 20:04:56
tags: lc
---

给定一个长字符串，该模板用于求解出某个子串，该子串满足一定的条件。我们使用滑动窗口的技术，并且有一个通用模板。

```
int findSubstring(string s){
        vector<int> map(128,0);
        int counter; // check whether the substring is valid
        int begin=0, end=0; //two pointers, one point to tail and one  head
        int d; //the length of substring

        for() { /* initialize the hash map here */ }

        while(end<s.size()){

            if(map[s[end++]]-- ?){  /* modify counter here */ }

            while(/* counter condition */){ 
                 
                 /* update d here if finding minimum*/

                //increase begin to make it invalid/valid again
                
                if(map[s[begin++]]++ ?){ /*modify counter here*/ }
            }  

            /* update d here if finding maximum*/
        }
        return d;
  }
```

启示：
1.双指针标记滑动窗口
2.count可以表示很多含义，例如已经找到的字符个数、重复的字符个数等，灵活运用之
3.?处的加减，取决于滑动窗口具体意义
4.注意最大值和最小值的更新地点
5.注意count的更新和滑动窗口含义的匹配
使用模板来解```最小窗口子串 76. Minimum Window Substring```
```
class Solution {
public:
    string minWindow(string s, string t) {
        vector<int> mp(256, 0);
        int begin , end, count, md;
        begin = end = count = 0;
        md = INT_MAX;
        for (auto c : t) {
            mp[c]++;
        }
        string res;
        while (end < s.size()) {
            if (mp[s[end++]]-->0) count++;
            
            while (count == t.size()) {
                if (md > end-begin) {
                    res = s.substr(begin, end-begin);
                    md = end-begin;
                }
                
                if (++mp[s[begin++]] > 0) count--;
            }
        }
        
        return res;
        
    } 
};
```

使用模板来解```最长不重复子串问题```
```
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        vector<int> mp(256, 0);
        int begin , end, count, md;
        begin = end = count = 0;
        md = INT_MIN;
        
        string res;
        while (end < s.size()) {
            if (mp[s[end++]]++>0) count++;
            
            while (count > 0) {
                if (mp[s[begin++]]-- > 1) count--;
            }
            if (md < end-begin) {
                    res = s.substr(begin, end-begin);
                    md = end-begin;
            }
        }
        return res.size();
    }
};
```

这里可以发现,count代表当前滑动窗口中，重复字符的个数。
相应地，滑动窗口表示当前每个字符的出现个数。


















