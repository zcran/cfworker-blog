---
title: "leetcode-滑动窗口95"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计满足 K 约束的子字符串数量 II

给你一个 二进制 字符串 s 和一个整数 k。

另给你一个二维整数数组 queries ，其中 queries[i] = [li, ri] 。

如果一个 二进制字符串 满足以下任一条件，则认为该字符串满足 k 约束：

字符串中 0 的数量最多为 k。
字符串中 1 的数量最多为 k。

返回一个整数数组 answer ，其中 answer[i] 表示 s[li..ri] 中满足 k 约束 的 子字符串 的数量。


```
impl Solution {
    pub fn count_k_constraint_substrings(s: String, k: i32, queries: Vec<Vec<i32>>) -> Vec<i64> {
        let s = s.as_bytes();
        let n = s.len();
        let k = k as usize;

        // right[i] 表示以 i 为起点的最长有效子数组的右端点 + 1
        let mut right = vec![n; n];
        let mut prefix = vec![0i64; n + 1];
        let mut freq = [0, 0];
        let mut left = 0;

        // 滑动窗口计算所有起点对应的最长有效长度
        for right_end in 0..n {
            let ch = (s[right_end] - b'0') as usize;
            freq[ch] += 1;

            // 当窗口同时包含超过 k 个 0 和超过 k 个 1 时收缩
            while freq[0] > k && freq[1] > k {
                let left_ch = (s[left] - b'0') as usize;
                freq[left_ch] -= 1;
                right[left] = right_end; // 记录 left 起点的最长有效右边界
                left += 1;
            }

            // 前缀和：以 right_end 结尾的所有有效子数组数量
            prefix[right_end + 1] = prefix[right_end] + (right_end - left + 1) as i64;
        }

        // 处理剩余的 left 指针（窗口一直有效直到末尾）
        while left < n {
            right[left] = n;
            left += 1;
        }

        // 处理每个查询
        let mut ans = Vec::with_capacity(queries.len());
        for query in queries {
            let l = query[0] as usize;
            let r = query[1] as usize;

            // 在 [l, r] 范围内，找到第一个右边界受限的位置
            let boundary = right[l].min(r + 1);

            // 第一部分：从 l 开始的子数组，右端点在 [l, boundary-1]
            // 这些子数组长度从 1 到 boundary-l
            let len = (boundary - l) as i64;
            let part1 = len * (len + 1) / 2;

            // 第二部分：右端点在 [boundary, r] 的子数组
            let part2 = prefix[r + 1] - prefix[boundary];

            ans.push(part1 + part2);
        }

        ans
    }
}
```
