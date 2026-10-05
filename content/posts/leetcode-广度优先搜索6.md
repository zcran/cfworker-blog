---
title: "leetcode-广度优先搜索6"
date: 2026-09-13T09:32:06+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## N 叉树的层序遍历

给定一个 N 叉树，返回其节点值的层序遍历。（即从左到右，逐层遍历）。

树的序列化输入是用层序遍历，每组子节点都由 null 值分隔（参见示例）。

C++
```
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        if (!root) return {};

        vector<vector<int>> ans;
        queue<Node*> q;
        q.push(root);

        // 每次处理一整层，逐层收集节点值
        while (!q.empty()) {
            int cnt = q.size();
            vector<int> level;
            level.reserve(cnt); // 预分配，避免扩容

            while (cnt--) {
                Node* cur = q.front();
                q.pop();
                level.push_back(cur->val);

                // 将当前节点的所有子节点入队（下一层）
                for (Node* child : cur->children) {
                    q.push(child);
                }
            }

            ans.push_back(std::move(level)); // 移动语义，避免拷贝
        }

        return ans;
    }
};
```
