---
title: "leetcode-动态规划30"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最优除法

给定一正整数数组 nums，nums 中的相邻整数将进行浮点除法。

例如，nums = [2,3,4]，我们将求表达式的值 "2/3/4"。

但是，你可以在任意位置添加任意数目的括号，来改变算数的优先级。你需要找出怎么添加括号，以便计算后的表达式的值为最大值。

以字符串格式返回具有最大值的对应表达式。

注意：你的表达式不应该包含多余的括号。


```
impl Solution {
    pub fn optimal_division(nums: Vec<i32>) -> String {
        match nums.as_slice() {
            // 只有一个数字，直接返回
            [x] => x.to_string(),
            // 两个数字，必须按原顺序相除
            [a, b] => format!("{a}/{b}"),
            // 三个及以上：第一个数字除以剩余所有数字的连除结果（即把剩余部分整体括起来），
            // 这样能使结果最大，且没有多余括号
            _ => {
                let rest = nums[1..]
                    .iter()
                    .map(|x| x.to_string())
                    .collect::<Vec<_>>()
                    .join("/");
                format!("{}/({})", nums[0], rest)
            }
        }
    }
}
```
