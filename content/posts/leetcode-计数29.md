---
title: "leetcode-计数29"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 检查数组对是否可以被 k 整除

给你一个整数数组 arr 和一个整数 k ，其中数组长度是偶数，值为 n 。

现在需要把数组恰好分成 n / 2 对，以使每对数字的和都能够被 k 整除。

如果存在这样的分法，请返回 true ；否则，返回 false。




```
impl Solution {
    pub fn can_arrange(arr: Vec<i32>, k: i32) -> bool {
        // 统计每个余数出现的次数
        let mut cnt = vec![0; k as usize];
        for &x in &arr {
            // 处理负余数：让余数落在 [0, k-1] 范围内
            let r = ((x % k) + k) % k;
            cnt[r as usize] += 1;
        }

        // 检查配对条件
        for r in 1..=(k / 2) as usize {
            let l = (k as usize - r) % k as usize; // 对称余数
            if r == l {
                // 当余数等于自身（即 k 为偶数且 r == k/2）
                if cnt[r] % 2 != 0 {
                    return false;
                }
            } else if cnt[r] != cnt[l] {
                return false;
            }
        }

        // 余数为 0 的数必须成对出现
        cnt[0] % 2 == 0
    }
}
```
