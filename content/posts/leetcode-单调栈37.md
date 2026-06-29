---
title: "leetcode-单调栈37"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 元素值大于变化阈值的子数组

给你一个整数数组 nums 和一个整数 threshold 。

找到长度为 k 的 nums 子数组，满足数组中 每个 元素都 大于 threshold / k 。

请你返回满足要求的 任意 子数组的 大小 。如果没有这样的子数组，返回 -1 。

子数组 是数组中一段连续非空的元素序列。


```
impl Solution {
    pub fn valid_subarray_size(nums: Vec<i32>, threshold: i32) -> i32 {
        let n = nums.len();
        let threshold = threshold as i64;

        // 并查集：parent[i] 表示 i 的父节点，初始指向自己
        let mut parent: Vec<usize> = (0..=n).collect();
        let mut size = vec![0; n + 1];

        // 查找函数（带路径压缩）
        fn find(x: usize, parent: &mut Vec<usize>) -> usize {
            if parent[x] != x {
                parent[x] = find(parent[x], parent);
            }
            parent[x]
        }

        // 按值降序排序索引
        let mut indices: Vec<usize> = (0..n).collect();
        indices.sort_unstable_by(|&a, &b| nums[b].cmp(&nums[a]));

        // 从大到小处理每个元素
        for &i in &indices {
            // 找到 i+1 所在连通块的根（右侧）
            let j = find(i + 1, &mut parent);
            // 将 i 合并到右侧连通块
            parent[i] = j;
            // 更新连通块大小（当前元素自身 + 右侧连通块大小）
            size[j] += size[i] + 1;

            // 检查条件：当前元素作为最小值，连通块大小是否满足要求
            if (nums[i] as i64) * (size[j] as i64) > threshold {
                return size[j] as i32;
            }
        }

        -1
    }
}
```
