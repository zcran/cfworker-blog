---
title: "leetcode-计数112"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 将数组按照奇偶性转化

给你一个整数数组 nums。请你按照以下顺序 依次 执行操作，转换 nums：

1. 将每个偶数替换为 0。
2. 将每个奇数替换为 1。
3. 按 非递减 顺序排序修改后的数组。

执行完这些操作后，返回结果数组。


```
impl Solution {
    pub fn transform_array(mut nums: Vec<i32>) -> Vec<i32> {
        let mut cnt = [0; 2];
        for &x in &nums { cnt[(x & 1) as usize] += 1; }
        nums.fill(0);
        for i in cnt[0]..nums.len() { nums[i] = 1; }
        nums
    }
}
```
