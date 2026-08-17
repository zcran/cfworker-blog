---
title: "leetcode-模拟84"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 分割数组中数字的数位

给你一个正整数数组 nums ，请你返回一个数组 answer ，你需要将 nums 中每个整数进行数位分割后，按照 nums 中出现的 相同顺序 放入答案数组中。

对一个整数进行数位分割，指的是将整数各个数位按原本出现的顺序排列成数组。

比方说，整数 10921 ，分割它的各个数位得到 [1,0,9,2,1] 。


```
impl Solution {
  /// 将每个整数按数位分割，保持原顺序放入结果数组。
  pub fn separate_digits(nums: Vec<i32>) -> Vec<i32> {
      let mut ans = Vec::with_capacity(nums.len() * 10);
      for &x in &nums {
          let mut n = x;
          let mut buf = [0; 10]; // i32 最多 10 位，栈数组避免堆分配
          let mut len = 0;
          while n > 0 {
              buf[len] = n % 10;
              n /= 10;
              len += 1;
          }
          ans.extend(buf[..len].iter().rev().copied());
      }
      ans
  }
}
```
