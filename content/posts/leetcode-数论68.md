---
title: "leetcode-数论68"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 互质元素的最大得分

给你一个长度为 n 的整数数组 nums 和一个整数 maxVal。

你 可以 将 nums 中的任意元素更改为 小于或等于 maxVal 的任意正整数。每次这样的更改代价为 1。

如果两个整数的 最大公约数（GCD） 为 1，则这两个整数 互质。

在所有修改之后，你 必须 选择一个下标 i，使得 nums[i] 与所有其他元素 nums[j] 互质。

令：

· selectedValue 为修改后 nums[i] 的最终值。
· modificationCost 为更改的元素总数。

得分定义为 score = selectedValue - modificationCost

返回 最大 可能得分。

gcd(a, b) 表示 a 和 b 的 最大公约数。


```
use std::sync::OnceLock;

const MX: usize = 100_001;

/// 预计算结构：莫比乌斯函数和质数列表
struct Precomputed {
    mu: [i32; MX],       // 莫比乌斯函数
    primes: Vec<usize>,  // 所有质数
}

static PRECOMP: OnceLock<Precomputed> = OnceLock::new();

fn precompute() -> &'static Precomputed {
    PRECOMP.get_or_init(|| {
        let mut mu = [0; MX];
        let mut primes = Vec::with_capacity(9600);
        let mut is_comp = [false; MX];

        mu[1] = 1;
        for i in 2..MX {
            if !is_comp[i] {
                primes.push(i);
                mu[i] = -1;
            }
            for &p in &primes {
                let t = i * p;
                if t >= MX { break; }
                is_comp[t] = true;
                if i % p == 0 {
                    mu[t] = 0;
                    break;
                } else {
                    mu[t] = -mu[i];
                }
            }
        }
        Precomputed { mu, primes }
    })
}

impl Solution {
    /// 返回最大得分
    pub fn max_score(nums: Vec<i32>, max_val: i32) -> i32 {
        let pre = precompute();
        let n = nums.len() as i32;
        let max_val = max_val as usize;
        let max_num = *nums.iter().max().unwrap() as usize;
        let limit = max_num.max(max_val).min(MX - 1); // 不超过预计算范围

        // 统计频率
        let mut cnt = vec![0; limit + 1];
        for &x in &nums {
            cnt[x as usize] += 1;
        }

        // 计算 cnt_multi[i] = 数组中 i 的倍数的个数
        let mut cnt_multi = cnt.clone();
        for &p in &pre.primes {
            if p > limit { break; }
            // 从大到小累加，使得 cnt_multi[i] 包含所有 i*p^k 的贡献
            for i in (1..=limit / p).rev() {
                cnt_multi[i] += cnt_multi[i * p];
            }
        }

        // 应用莫比乌斯函数：f[i] = mu[i] * cnt_multi[i]
        let mut f = vec![0; limit + 1];
        for i in 1..=limit {
            f[i] = pre.mu[i] * cnt_multi[i];
        }

        // 狄利克雷后缀和：f[i] 变为与 i 互质的元素个数
        for &p in &pre.primes {
            if p > limit { break; }
            for i in 1..=limit / p {
                f[i * p] += f[i];
            }
        }

        let total = cnt_multi[1]; // 总元素数

        // 处理 selected = 1 的特殊情况
        let mut ans = if cnt[1] > 0 { 1 } else { 0 };

        // 从大到小枚举 selected_value，剪枝优化
        let upper = limit.min(max_val.max(max_num));
        for selected in (2..=upper).rev() {
            if selected as i32 <= ans { break; }

            // 如果 selected > max_val 且不在 nums 中，无法通过修改得到
            if selected > max_val && cnt[selected] == 0 {
                continue;
            }

            // 与 selected 不互质的元素数 = 总数 - 互质数
            let not_coprime = total - f[selected];
            let mut cost = not_coprime;

            // 如果 selected 存在于 nums 中，可保留一个，减少一次修改
            if selected <= max_num && cnt[selected] > 0 {
                // selected 与自身不互质（gcd=selected），但自身无需修改
                cost -= 1;
            } else if cost == 0 {
                // 所有元素都与 selected 互质，但必须改一个数成 selected
                cost = 1;
            }

            // 确保 cost 非负
            if cost < 0 { cost = 0; }

            ans = ans.max(selected as i32 - cost);
        }

        ans
    }
}
```
