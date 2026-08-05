---
title: "leetcode-回溯41"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 循环码排列

给你两个整数 n 和 start。你的任务是返回任意 (0,1,2,,...,2^n-1) 的排列 p，并且满足：

p[0] = start
p[i] 和 p[i+1] 的二进制表示形式只有一位不同
p[0] 和 p[2^n -1] 的二进制表示形式也只有一位不同


```
impl Solution {
    pub fn circular_permutation(n: i32, start: i32) -> Vec<i32> {
        // 生成 n 位格雷码序列（长度 2^n）
        let mut gray = vec![0; 1 << n];
        for i in 0..gray.len() {
            gray[i] = (i ^ (i >> 1)) as i32;
        }

        // 找到 start 在格雷码中的位置并旋转
        let pos = gray.iter().position(|&x| x == start).unwrap();
        gray.rotate_left(pos);

        gray
    }
}
```
