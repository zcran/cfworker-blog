---
title: "leetcode-广度优先搜索12"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## N 叉树的最大深度

给定一个 N 叉树，找到其最大深度。

最大深度是指从根节点到最远叶子节点的最长路径上的节点总数。

N 叉树输入按层序遍历序列化表示，每组子节点由空值分隔。


C++
```
class Solution {
public:
    int maxDepth(Node* root) {
        if (root == nullptr) {
            return 0;
        }
        // 当前节点的深度 = 所有子树最大深度 + 1（自身）
        int max_child_depth = 0;
        for (Node* child : root->children) {
            max_child_depth = max(max_child_depth, maxDepth(child));
        }
        return max_child_depth + 1;
    }
};
```
