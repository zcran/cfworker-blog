---
title: "leetcode-位掩码30"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 执行操作后的最大分割数量

给定一个字符串 s 和一个整数 k。

首先，你最多可以更改 s 中的 一处 下标对应字符为另一个小写英文字母。

之后，执行以下分割操作，直到 s 变为 空串：

· 选择 s 的最长 前缀，该前缀最多包含 k 个 不同 字符。
· 从 s 中 删除 这个前缀，并将分割数量加一。如果有剩余字符，它们在 s 中保持原来的顺序。

返回一个整数，表示在 最多 改变一处下标对应字符的情况下，经过操作后得到的最大分割数。


```
impl Solution {
    /// 计算至多修改一个字符后，字符串能分割成的最大段数（每段不同字符数 ≤ k）
    pub fn max_partitions_after_operations(s: String, k: i32) -> i32 {
        let k = k as u32;
        let s = s.as_bytes();
        let n = s.len();
        // 预计算每个字符的位掩码
        let bit: Vec<u32> = s.iter().map(|&b| 1 << (b - b'a')).collect();

        // left[i] = (已完成段数, 当前段掩码, 当前段不同字符数)  索引 0..n
        let mut left = vec![[0u32; 3]; n + 1];
        let (mut num, mut mask, mut cnt) = (0, 0, 0);
        for i in 0..n {
            let b = bit[i];
            if mask & b == 0 {
                cnt += 1;
                if cnt <= k {
                    mask |= b;
                } else {
                    num += 1;
                    mask = b;
                    cnt = 1;
                }
            }
            left[i + 1] = [num, mask, cnt];
        }

        // right[i] = (从 i 向后开始的已完成段数, 当前段掩码, 当前段不同字符数)  索引 0..n
        let mut right = vec![[0u32; 3]; n + 1];
        let (mut num, mut mask, mut cnt) = (0, 0, 0);
        for i in (0..n).rev() {
            let b = bit[i];
            if mask & b == 0 {
                cnt += 1;
                if cnt <= k {
                    mask |= b;
                } else {
                    num += 1;
                    mask = b;
                    cnt = 1;
                }
            }
            right[i] = [num, mask, cnt];
        }

        let mut ans = 0;
        for i in 0..n {
            // i 位置左右的分段信息（不包括 i 本身）
            let l = left[i];
            let r = right[i + 1];
            let seg_num = l[0] + r[0] + 2; // 左右段数 + 中间单独一段（修改后）
            let total_mask = l[1] | r[1];
            let total_cnt = total_mask.count_ones();

            // 根据左右段是否可以合并来调整段数
            if l[2] == k && r[2] == k && total_cnt < 26 {
                // 左右段都已满，且合并后仍 ≤ 26，则合并可减少一段（实际效果是增加总段数？原代码逻辑）
                ans = ans.max(seg_num + 1);
            } else if (total_cnt + 1).min(26) <= k {
                // 合并后加上修改字符仍 ≤ k，则中间段可以和左右合并，减少一段
                ans = ans.max(seg_num - 1);
            } else {
                ans = ans.max(seg_num);
            }
        }
        ans as i32
    }
}
```
