---
title: "Leetcode 后缀数组1"
date: 2023-12-19T15:46:47+08:00
tags: ["leetcode", "后缀数组"]
draft: false
---

## 构造字符串的总得分和

你需要从空字符串开始 构造 一个长度为 n 的字符串 s ，构造的过程为每次给当前字符串 前面 添加 一个 字符。构造过程中得到的所有字符串编号为 1 到 n ，其中长度为 i 的字符串编号为 si 。

比方说，s = "abaca" ，s1 == "a" ，s2 == "ca" ，s3 == "aca" 依次类推。
si 的 得分 为 si 和 sn 的 最长公共前缀 的长度（注意 s == sn ）。

给你最终的字符串 s ，请你返回每一个 si 的 得分之和 。

```
class Solution {
public:
    long long sumScores(string s) {
        int n = s.length();
        long ans = n;
        vector<int> z(n);
        for (int i = 1, l = 0, r = 0; i < n; ++i) {
            z[i] = max(min(z[i - l], r - i + 1), 0);
            while (i + z[i] < n && s[z[i]] == s[i + z[i]]) {
                l = i;
                r = i + z[i];
                ++z[i];
            }
            ans += z[i];
        }
        return ans;
    }
};
```