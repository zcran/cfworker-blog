---
title: "leetcode-回溯55"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 下一个更大的数值平衡数

如果整数  x 满足：对于每个数位 d ，这个数位 恰好 在 x 中出现 d 次。那么整数 x 就是一个 数值平衡数 。

给你一个整数 n ，请你返回 严格大于 n 的 最小数值平衡数 。


```
impl Solution {
    pub fn next_beautiful_number(n: i32) -> i32 {
        // 从 n+1 开始逐个检查
        let mut x = n + 1;
        loop {
            // 统计每个数字出现的次数
            let mut count = [0; 10];
            let mut temp = x;
            while temp > 0 {
                count[(temp % 10) as usize] += 1;
                temp /= 10;
            }

            // 验证：每个数字 d 必须恰好出现 d 次
            let mut valid = true;
            let mut temp = x;
            while temp > 0 {
                let d = (temp % 10) as usize;
                if count[d] != d {
                    valid = false;
                    break;
                }
                temp /= 10;
            }

            if valid {
                return x;
            }
            x += 1;
        }
    }
}
```
