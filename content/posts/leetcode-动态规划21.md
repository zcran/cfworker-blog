---
title: "leetcode-动态规划21"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 旋转函数

给定一个长度为 n 的整数数组 nums 。

假设 arrk 是数组 nums 顺时针旋转 k 个位置后的数组，我们定义 nums 的 旋转函数  F 为：

F(k) = 0 * arrˇk[0] + 1 * arrˇk[1] + ... + (n - 1) * arrˇk[n - 1]
返回 F(0), F(1), ..., F(n-1)中的最大值 。

生成的测试用例让答案符合 32 位 整数。



```
impl Solution {
    pub fn max_rotate_function(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 用 i64 累加，避免中间结果溢出 i32
        let (mut f, sum) = nums.iter().enumerate().fold((0i64, 0i64), |(f, s), (i, &v)| {
            (f + i as i64 * v as i64, s + v as i64)
        });

        // 递推 F(k) = F(k-1) + sum - n * nums[n-k]，边迭代边取最大值
        let mut ans = f;
        for k in 1..n {
            f += sum - n as i64 * nums[n - k] as i64;
            ans = ans.max(f);
        }

        // 题目保证最终答案在 i32 范围内，安全转换
        ans as i32
    }
}
```
