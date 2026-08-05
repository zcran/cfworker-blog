---
title: "leetcode-回溯35"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 连续差相同的数字

返回所有长度为 n 且满足其每两个连续位上的数字之间的差的绝对值为 k 的 非负整数 。

请注意，除了 数字 0 本身之外，答案中的每个数字都 不能 有前导零。例如，01 有一个前导零，所以是无效的；但 0 是有效的。

你可以按 任何顺序 返回答案。


```
impl Solution {
    /// 返回所有长度为 n 且相邻数字差的绝对值为 k 的数字
    ///
    /// 思路：DFS 从首位开始逐位构建，每个位置尝试 ±k 两种选择
    pub fn nums_same_consec_diff(n: i32, k: i32) -> Vec<i32> {
        // 特殊情况：n=1 时，0-9 都满足
        if n == 1 {
            return (0..=9).collect();
        }

        let mut result = Vec::new();

        /// 深度优先搜索构建数字
        /// - num: 当前已构建的数字
        /// - last: 上一位数字
        /// - remaining: 剩余需要构建的位数
        fn dfs(num: i32, last: i32, remaining: i32, k: i32, result: &mut Vec<i32>) {
            // 构建完成，加入结果
            if remaining == 0 {
                result.push(num);
                return;
            }

            // 尝试加上 k（如果不超过 9）
            let next = last + k;
            if next <= 9 {
                dfs(num * 10 + next, next, remaining - 1, k, result);
            }

            // 尝试减去 k（如果 k != 0 且不小于 0）
            let next = last - k;
            if k != 0 && next >= 0 {
                dfs(num * 10 + next, next, remaining - 1, k, result);
            }
        }

        // 首位不能为 0，从 1-9 开始
        for first in 1..=9 {
            dfs(first, first, n - 1, k, &mut result);
        }

        result
    }
}
```
