---
title: "leetcode-模拟100"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 双模幂运算

给你一个下标从 0 开始的二维数组 variables ，其中 variables[i] = [ai, bi, ci, mi]，以及一个整数 target 。

如果满足以下公式，则下标 i 是 好下标：

0 <= i < variables.length

((ai^bi % 10)^ci) % mi == target

返回一个由 好下标 组成的数组，顺序不限 。


```
fn pow_mod(mut base: i32, mut exp: i32, modulus: i32) -> i32 {
  let mut res = 1;
  while exp > 0 {
      if exp & 1 == 1 {
          res = res * base % modulus;
      }
      base = base * base % modulus;
      exp >>= 1;
  }
  res
}

impl Solution {
  pub fn get_good_indices(variables: Vec<Vec<i32>>, target: i32) -> Vec<i32> {
      let mut ans = Vec::new();

      for (i, v) in variables.iter().enumerate() {
          // 解构 [ai, bi, ci, mi]，长度不对则跳过
          let [a, b, c, m] = v.as_slice() else { continue };
          // 好下标条件: ((a^b % 10)^c) % m == target
          if pow_mod(pow_mod(*a, *b, 10), *c, *m) == target {
              ans.push(i as i32);
          }
      }

      ans
  }
}
```
