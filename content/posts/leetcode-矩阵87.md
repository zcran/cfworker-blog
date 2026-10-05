---
title: "leetcode-矩阵85"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 排序矩阵查找

给定 M×N 矩阵，每一行、每一列都按升序排列，请编写代码找出某元素。

示例：

现有矩阵 matrix 如下：

[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
给定 target = 5，返回 true。

给定 target = 20，返回 false。

C++
```
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        // 逐行二分查找：每行都是升序，整体 O(M log N)
        for (const auto& row : matrix) {
            if (binary_search(row.begin(), row.end(), target)) {
                return true;
            }
        }
        return false;
    }
};
```
