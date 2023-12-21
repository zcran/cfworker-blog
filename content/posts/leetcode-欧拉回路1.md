---
title: "Leetcode 欧拉回路1"
date: 2023-12-19T15:20:02+08:00
tags: ["leetcode", "欧拉回路"]
draft: false
---

## 合法重新排列数对

给你一个下标从 0 开始的二维整数数组 pairs ，其中 pairs[i] = [start⌄i, end⌄i] 。如果 pairs 的一个重新排列，满足对每一个下标 i （ 1 <= i < pairs.length ）都有 end⌄i-1 == start⌄i ，那么我们就认为这个重新排列是 pairs 的一个 合法重新排列 。

请你返回 任意一个 pairs 的合法重新排列。

注意：数据保证至少存在一个 pairs 的合法重新排列。

```
class Solution {
public:
    vector<vector<int>> validArrangement(vector<vector<int>>& pairs) {
        // 存储图
        unordered_map<int, vector<int>> edges;
        // 存储入度和出度
        unordered_map<int, int> indeg, outdeg;
        for (const auto& p: pairs) {
            int x = p[0], y = p[1];
            edges[x].push_back(y);
            ++indeg[y];
            ++outdeg[x];
        }
        
        // 寻找起始节点
        int start = pairs[0][0];
        for (const auto& [x, occ]: outdeg) {
            // 如果有节点出度比入度恰好多 1，那么只有它才能是起始节点
            if (occ == indeg[x] + 1) {
                start = x;
                break;
            }
        }
        
        vector<vector<int>> ans;
        
        // 深度优先搜索（Hierholzer 算法）求解欧拉通路
        function<void(int)> dfs = [&](int u) {
            while (!edges[u].empty()) {
                int v = edges[u].back();
                edges[u].pop_back();
                dfs(v);
                ans.push_back({u, v});
            }
        };
        
        dfs(start);
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```