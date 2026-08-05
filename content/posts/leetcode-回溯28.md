---
title: "leetcode-回溯28"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 24 点游戏

给定一个长度为4的整数数组 cards 。你有 4 张卡片，每张卡片上都包含一个范围在 [1,9] 的数字。您应该使用运算符 ['+', '-', '*', '/'] 和括号 '(' 和 ')' 将这些卡片上的数字排列成数学表达式，以获得值24。

你须遵守以下规则:

· 除法运算符 '/' 表示实数除法，而不是整数除法。
    · 例如， 4 /(1 - 2 / 3)= 4 /(1 / 3)= 12 。

· 每个运算都在两个数字之间。特别是，不能使用 “-” 作为一元运算符。
    · 例如，如果 cards =[1,1,1,1] ，则表达式 “-1 -1 -1 -1” 是 不允许 的。

· 你不能把数字串在一起
    · 例如，如果 cards =[1,2,1,2] ，则表达式 “12 + 12” 无效。

如果可以得到这样的表达式，其计算结果为 24 ，则返回 true ，否则返回 false 。


```
impl Solution {
    /// 判断能否用 4 张牌通过四则运算得到 24
    ///
    /// 思路：任选两张牌进行运算，合并成一张新牌，递归直到只剩 1 张牌
    /// 每次有 6 种运算组合：a+b, a-b, b-a, a*b, a/b, b/a
    pub fn judge_point24(cards: Vec<i32>) -> bool {
        const EPS: f64 = 1e-9;

        fn dfs(nums: &mut Vec<f64>) -> bool {
            let n = nums.len();

            // 只剩一张牌时，检查是否等于 24
            if n == 1 {
                return (nums[0] - 24.0).abs() < EPS;
            }

            // 枚举所有不同的位置组合 (i, j)，避免重复
            for i in 0..n {
                for j in i + 1..n {
                    let a = nums[i];
                    let b = nums[j];

                    // 收集所有可能的运算结果
                    let results = [
                        a + b,
                        a - b,
                        b - a,
                        a * b,
                        if b.abs() > EPS { a / b } else { f64::NAN },
                        if a.abs() > EPS { b / a } else { f64::NAN },
                    ];

                    // 尝试每个运算结果
                    for &res in &results {
                        if res.is_nan() {
                            continue;
                        }

                        // 移除 j，用 i 位置保存结果，实现原地修改
                        nums[i] = res;
                        nums.remove(j);

                        if dfs(nums) {
                            return true;
                        }

                        // 恢复状态
                        nums.insert(j, b);
                        nums[i] = a;
                    }
                }
            }

            false
        }

        let mut nums: Vec<f64> = cards.into_iter().map(|x| x as f64).collect();
        dfs(&mut nums)
    }
}
```
