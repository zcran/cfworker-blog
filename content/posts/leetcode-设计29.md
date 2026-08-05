---
title: "leetcode-设计29"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 设计一个 ATM 机器

一个 ATM 机器，存有 5 种面值的钞票：20 ，50 ，100 ，200 和 500 美元。初始时，ATM 机是空的。用户可以用它存或者取任意数目的钱。

取款时，机器会优先取 较大 数额的钱。

· 比方说，你想取 $300 ，并且机器里有 2 张 $50 的钞票，1 张 $100 的钞票和1 张 $200 的钞票，那么机器会取出 $100 和 $200 的钞票。
· 但是，如果你想取 $600 ，机器里有 3 张 $200 的钞票和1 张 $500 的钞票，那么取款请求会被拒绝，因为机器会先取出 $500 的钞票，然后无法取出剩余的 $100 。注意，因为有 $500 钞票的存在，机器 不能 取 $200 的钞票。

请你实现 ATM 类：

· ATM() 初始化 ATM 对象。
· void deposit(int[] banknotesCount) 分别存入 $20 ，$50，$100，$200 和 $500 钞票的数目。
· int[] withdraw(int amount) 返回一个长度为 5 的数组，分别表示 $20 ，$50，$100 ，$200 和 $500 钞票的数目，并且更新 ATM 机里取款后钞票的剩余数量。如果无法取出指定数额的钱，请返回 [-1] （这种情况下 不 取出任何钞票）。


```
/// ATM 自动取款机
///
/// 支持存款和取款，取款时优先使用大面额钞票（贪心策略）
/// 时间复杂度: deposit O(1), withdraw O(5) ≈ O(1)
/// 空间复杂度: O(1)
struct ATM {
    counts: [i64; 5],     // 每种面额的钞票数量 [20, 50, 100, 200, 500]
    denoms: [i64; 5],     // 面额值
}

impl ATM {
    /// 初始化 ATM，所有钞票数量为 0
    fn new() -> Self {
        ATM {
            counts: [0; 5],
            denoms: [20, 50, 100, 200, 500],
        }
    }

    /// 存入钞票
    fn deposit(&mut self, banknotes_count: Vec<i32>) {
        for i in 0..5 {
            self.counts[i] += banknotes_count[i] as i64;
        }
    }

    /// 取款，优先使用大面额钞票
    ///
    /// # 返回
    /// - 成功：长度为 5 的数组，表示每种面额取出的数量
    /// - 失败：[-1]
    fn withdraw(&mut self, mut amount: i32) -> Vec<i32> {
        let mut result = [0; 5];
        let mut remaining = amount as i64;

        // 从大到小尝试使用钞票
        for i in (0..5).rev() {
            // 最多能取多少张当前面额的钞票
            let max_use = self.counts[i].min(remaining / self.denoms[i]);
            result[i] = max_use as i32;
            remaining -= max_use * self.denoms[i];
        }

        // 无法凑出指定金额
        if remaining > 0 {
            return vec![-1];
        }

        // 成功：更新库存并返回结果
        for i in 0..5 {
            self.counts[i] -= result[i] as i64;
        }
        result.to_vec()
    }
}
```
