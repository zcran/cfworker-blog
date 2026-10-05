---
title: "leetcode-位运算24"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 可被 5 整除的二进制前缀

给定一个二进制数组 nums ( 索引从0开始 )。

我们将xi 定义为其二进制表示形式为子数组 nums[0..i] (从最高有效位到最低有效位)。

例如，如果 nums =[1,0,1] ，那么 x0 = 1, x1 = 2, 和 x2 = 5。

返回布尔值列表 answer，只有当 xi 可以被 5 整除时，答案 answer[i] 为 true，否则为 false。


```
impl Solution {
    /// 返回每个前缀二进制数是否能被 5 整除。
    ///
    /// 遍历时只需维护当前前缀对 5 取模的结果：
    /// 每读入一位，`rem = (rem * 2 + bit) % 5`。
    /// 这样避免了构造可能溢出的完整二进制数。
    pub fn prefixes_div_by5(nums: Vec<i32>) -> Vec<bool> {
        let mut rem = 0;
        nums.into_iter()
            .map(|bit| {
                rem = (rem * 2 + bit) % 5;
                rem == 0
            })
            .collect()
    }
}
```
