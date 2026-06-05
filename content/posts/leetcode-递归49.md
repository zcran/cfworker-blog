---
title: "leetcode-递归49"
date: 2026-05-26T10:00:19+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 2出现的次数


编写一个方法，计算从 0 到 n (含 n) 中数字 2 出现的次数。


```
impl Solution {
    // 数学方法：逐位计算
    pub fn number_of2s_in_range(n: i32) -> i32 {
        if n < 2 {
            return 0;
        }

        let mut count = 0;
        let mut factor = 1;  // 当前位权值：1, 10, 100, ...
        let mut num = n;

        while num > 0 {
            let digit = num % 10;           // 当前位数字
            let higher = num / 10;          // 高位部分
            let lower = n % factor;         // 低位部分

            // 计算当前位出现2的次数
            // 高位贡献：higher * factor
            count += higher * factor;

            if digit == 2 {
                // 当前位为2时，加上低位+1
                count += lower + 1;
            } else if digit > 2 {
                // 当前位大于2时，加上完整的factor
                count += factor;
            }
            // 当前位小于2时，不需要额外添加

            factor *= 10;
            num = higher;
        }

        count
    }
}
```
