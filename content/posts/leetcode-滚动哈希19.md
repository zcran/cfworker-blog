---
title: "leetcode-滚动哈希19"
date: 2026-04-17T20:00:32+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 匹配模式数组的子数组数目 II

给你一个下标从 0 开始长度为 n 的整数数组 nums ，和一个下标从 0 开始长度为 m 的整数数组 pattern ，pattern 数组只包含整数 -1 ，0 和 1 。

大小为 m + 1 的子数组 nums[i..j] 如果对于每个元素 pattern[k] 都满足以下条件，那么我们说这个子数组匹配模式数组 pattern ：

如果 pattern[k] == 1 ，那么 nums[i + k + 1] > nums[i + k]
如果 pattern[k] == 0 ，那么 nums[i + k + 1] == nums[i + k]
如果 pattern[k] == -1 ，那么 nums[i + k + 1] < nums[i + k]
请你返回匹配 pattern 的 nums 子数组的 数目 。

```
impl Solution {
    /// 统计 nums 中满足 pattern 描述的相邻关系（1:上升, 0:相等, -1:下降）的子数组个数
    pub fn count_matching_subarrays(nums: Vec<i32>, pat: Vec<i32>) -> i32 {
        // ---------- 1. 将 nums 转换为差分符号序列（函数式风格） ----------
        // windows(2) 生成所有相邻对，map 将每对映射为 1 / 0 / -1
        let diff: Vec<i32> = nums
            .windows(2)
            .map(|w| {
                if w[1] > w[0] {
                    1
                } else if w[1] == w[0] {
                    0
                } else {
                    -1
                }
            })
            .collect();

        // ---------- 2. 构建 pattern 的 next 数组（KMP 部分匹配表） ----------
        let m = pat.len();
        let mut next = vec![0; m];
        let mut j = 0;
        for i in 1..m {
            while j > 0 && pat[i] != pat[j] {
                j = next[j - 1];
            }
            if pat[i] == pat[j] {
                j += 1;
            }
            next[i] = j;
        }

        // ---------- 3. 在 diff 上运行 KMP 搜索，统计匹配次数 ----------
        let mut res = 0;
        j = 0;
        for &d in &diff {
            while j > 0 && d != pat[j] {
                j = next[j - 1];
            }
            if d == pat[j] {
                j += 1;
                if j == m {
                    res += 1;
                    j = next[j - 1]; // 允许重叠匹配
                }
            }
        }
        res
    }
}
```
