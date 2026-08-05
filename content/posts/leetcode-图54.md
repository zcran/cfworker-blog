---
title: "leetcode-图54"
date: 2026-08-01T09:50:57+08:00
tags: ["leetcode", "图"]
draft: false
---


## 分割回文串

给定一个字符串 s ，请将 s 分割成一些子串，使每个子串都是 回文串 ，返回 s 所有可能的分割方案。

回文串 是正着读和反着读都一样的字符串。


```
class Solution {
private:
    // dp[i][j] 表示子串 s[i..j] 是否为回文串
    vector<vector<int>> dp;
    // 存储所有可能的分割方案
    vector<vector<string>> result;
    // 存储当前正在构建的一种分割方案
    vector<string> current;
    // 字符串长度
    int n;

public:
    /**
     * 深度优先搜索，枚举所有可能的分割方案
     * @param s 原始字符串
     * @param start 当前搜索的起始位置
     */
    void dfs(const string& s, int start) {
        // 如果已经到达字符串末尾，说明找到了一种完整的分割方案
        if (start == n) {
            result.push_back(current);
            return;
        }

        // 尝试从 start 开始，枚举所有可能的回文子串
        for (int end = start; end < n; ++end) {
            // 如果 s[start..end] 是回文串
            if (dp[start][end]) {
                // 将该回文子串加入当前方案
                current.push_back(s.substr(start, end - start + 1));
                // 递归处理剩余部分
                dfs(s, end + 1);
                // 回溯，移除刚刚加入的子串
                current.pop_back();
            }
        }
    }

    /**
     * 将字符串 s 分割成若干回文子串，返回所有可能的分割方案
     * @param s 待分割的字符串
     * @return 所有可能的分割方案
     */
    vector<vector<string>> partition(string s) {
        n = s.size();

        // 初始化 dp 表，所有子串默认为回文串（包括空串和单字符）
        dp.assign(n, vector<int>(n, true));

        // 动态规划预计算所有子串是否为回文串
        // dp[i][j] = true 当且仅当 s[i] == s[j] 且 dp[i+1][j-1] 为 true
        // 从后往前遍历，确保 dp[i+1][j-1] 已经计算过
        for (int i = n - 1; i >= 0; --i) {
            for (int j = i + 1; j < n; ++j) {
                // 长度为 2 时，dp[i+1][j-1] 对应空串，默认 true
                dp[i][j] = (s[i] == s[j]) && dp[i + 1][j - 1];
            }
        }

        // 从位置 0 开始 DFS 搜索
        dfs(s, 0);
        return result;
    }
};
```
