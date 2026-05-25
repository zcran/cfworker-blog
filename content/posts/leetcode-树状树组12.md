---
title: "leetcode-树状树组12"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 通过指令创建有序数组

给你一个整数数组 instructions ，你需要根据 instructions 中的元素创建一个有序数组。一开始你有一个空的数组 nums ，你需要 从左到右 遍历 instructions 中的元素，将它们依次插入 nums 数组中。每一次插入操作的 代价 是以下两者的 较小值 ：

nums 中 严格小于  instructions[i] 的数字数目。
nums 中 严格大于  instructions[i] 的数字数目。
比方说，如果要将 3 插入到 nums = [1,2,3,5] ，那么插入操作的 代价 为 min(2, 1) (元素 1 和  2 小于 3 ，元素 5 大于 3 ），插入后 nums 变成 [1,2,3,3,5] 。

请你返回将 instructions 中所有元素依次插入 nums 后的 总最小代价 。由于答案会很大，请将它对 109 + 7 取余 后返回。


```
/// 树状数组（Fenwick Tree），支持单点更新和前缀查询
struct BIT {
    tree: Vec<i32>, // 内部 1‑based 存储
}

impl BIT {
    /// 创建可存储 [0, n-1] 区间值的树状数组
    fn new(n: usize) -> Self {
        BIT {
            tree: vec![0; n + 1], // 索引 0 未使用
        }
    }

    /// 将位置 `pos`（0‑based）的值增加 `delta`
    fn update(&mut self, pos: usize, delta: i32) {
        let mut idx = pos + 1; // 转换到 1‑based
        while idx < self.tree.len() {
            self.tree[idx] += delta;
            idx += idx & (!idx + 1); // lowbit
        }
    }

    /// 查询区间 `[l, r]`（0‑based，闭区间）内元素个数
    fn range_query(&self, l: usize, r: usize) -> i32 {
        self.prefix(r + 1) - self.prefix(l)
    }

    /// 查询前 `len` 个元素的和（即 `[0, len-1]` 的和），len 表示 1‑based 长度
    fn prefix(&self, len: usize) -> i32 {
        let mut idx = len;
        let mut sum = 0;
        while idx > 0 {
            sum += self.tree[idx];
            idx -= idx & (!idx + 1);
        }
        sum
    }
}

impl Solution {
    /// 计算将 instructions 依次插入到空数组时，每次插入代价为 `min(左侧较小数的个数, 右侧较大数的个数)` 的总和
    /// 使用树状数组维护当前已插入元素的频次，O(N log M) 时间，M 为值域（100001）
    pub fn create_sorted_array(instructions: Vec<i32>) -> i32 {
        const MOD: i32 = 1_000_000_007;
        const MAX_VAL: usize = 100_001; // 值域上界

        let mut bit = BIT::new(MAX_VAL);
        instructions
            .iter()
            .fold(0, |acc, &val| {
                let val = val as usize;
                // 左侧严格小于 val 的个数 = 区间 [0, val-1]
                let left_count = bit.range_query(0, val.saturating_sub(1));
                // 右侧严格大于 val 的个数 = 区间 [val+1, MAX_VAL-1]
                let right_count = if val + 1 < MAX_VAL {
                    bit.range_query(val + 1, MAX_VAL - 1)
                } else {
                    0
                };
                let cost = left_count.min(right_count);
                bit.update(val, 1);
                (acc + cost) % MOD
            })
    }
}
```
