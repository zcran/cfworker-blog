---
title: "leetcode-回溯49"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 构建字典序最大的可行序列

给你一个整数 n ，请你找到满足下面条件的一个序列：

· 整数 1 在序列中只出现一次。
· 2 到 n 之间每个整数都恰好出现两次。
· 对于每个 2 到 n 之间的整数 i ，两个 i 之间出现的距离恰好为 i 。

序列里面两个数 a[i] 和 a[j] 之间的 距离 ，我们定义为它们下标绝对值之差 |j - i| 。

请你返回满足上述条件中 字典序最大 的序列。题目保证在给定限制条件下，一定存在解。

一个序列 a 被认为比序列 b （两者长度相同）字典序更大的条件是： a 和 b 中第一个不一样的数字处，a 序列的数字比 b 序列的数字大。比方说，[0,1,9,0] 比 [0,1,5,6] 字典序更大，因为第一个不同的位置是第三个数字，且 9 比 5 大。


```
impl Solution {
    pub fn construct_distanced_sequence(n: i32) -> Vec<i32> {
        let n = n as usize;
        let mut result = vec![0; 2 * n - 1];
        let mut used = vec![false; n + 1];
        Self::backtrack(n, 0, &mut result, &mut used);
        result
    }

    /// 回溯构造字典序最大的序列
    /// - n: 最大数字
    /// - idx: 当前要填充的位置
    /// - result: 构造中的序列
    /// - used: 数字是否已使用（1 只出现一次，2..n 各出现两次）
    fn backtrack(n: usize, idx: usize, result: &mut Vec<i32>, used: &mut Vec<bool>) -> bool {
        // 所有位置都已填充
        if idx == result.len() {
            return true;
        }

        // 当前位置已被占用，跳到下一位
        if result[idx] != 0 {
            return Self::backtrack(n, idx + 1, result, used);
        }

        // 从大到小尝试数字，保证字典序最大
        for num in (1..=n).rev() {
            if used[num] {
                continue;
            }

            if num == 1 {
                // 数字 1 只出现一次
                result[idx] = 1;
                used[1] = true;
                if Self::backtrack(n, idx + 1, result, used) {
                    return true;
                }
                result[idx] = 0;
                used[1] = false;
            } else {
                // 数字 num 出现两次，相隔 num 个位置
                let j = idx + num;
                if j < result.len() && result[j] == 0 {
                    // 放置两个 num
                    result[idx] = num as i32;
                    result[j] = num as i32;
                    used[num] = true;

                    if Self::backtrack(n, idx + 1, result, used) {
                        return true;
                    }

                    // 回溯
                    result[idx] = 0;
                    result[j] = 0;
                    used[num] = false;
                }
            }
        }

        false
    }
}
```
