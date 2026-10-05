---
title: "leetcode-贪心19"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最大交换

给定一个非负整数，你至多可以交换一次数字中的任意两位。返回你能得到的最大值。


```
impl Solution {
    pub fn maximum_swap(num: i32) -> i32 {
        let mut s = num.to_string().into_bytes();
        let n = s.len();

        // 从右向左扫描：
        //   max_idx：当前位置右侧（含自身）最大数字的下标
        //   当 s[i] < s[max_idx] 时，记录一次可交换的候选 (i, max_idx)
        // 由于从右向左遍历，最后记录的 p 是最靠左的交换位置，收益最大；
        // 同时 q 对应该位置的右侧最大数字，若存在多个相同最大值会保留最靠右的。
        let (mut max_idx, mut p, mut q) = (n - 1, n, n);

        for i in (0..n - 1).rev() {
            if s[i] > s[max_idx] {
                max_idx = i;
            } else if s[i] < s[max_idx] {
                p = i;
                q = max_idx;
            }
        }

        // p == n 说明序列已降序，无法交换出更大的数
        if p != n {
            s.swap(p, q);
        }

        // 全为 ASCII 数字，安全转换为字符串再解析
        std::str::from_utf8(&s).unwrap().parse().unwrap()
    }
}
```
