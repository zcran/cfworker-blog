---
title: "leetcode-位运算87"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 字符对转换后字典序最大的字符串

给你一个整数数组 nums。

对于 nums 中的每个整数 x，首先生成一个由 x 个小写字母 'a' 组成的字符串。

你可以执行以下操作任意次（包括零次）：

选择两个 相邻且相同 的字母，并将它们替换为字母表中的下一个字母。

例如，"aa" 可以替换为 "b"，"bb" 可以替换为 "c"。对 "zz" 则无法进行替换。

对于每个 x，请你确定可以获得的 字典序最大 的字符串。

返回一个字符串数组，其中第 i 个字符串是 nums[i] 的答案。

在两个字符串不同处的第一个位置，如果字符串 a 包含的字母在字母表中的顺序晚于 b 中的相应字母，则字符串 a 字典序大于 字符串 b。如果前 min(a.length, b.length) 个字符相同，则较长的字符串字典序更大。


```
impl Solution {
    pub fn largest_string(nums: Vec<i32>) -> Vec<String> {
        // 思路：
        // 对 x 个 'a'，反复合并相邻相同字母，本质上等价于：
        // 把 x 看作二进制数，每一位对应一个字母（第 i 位 -> 字母 'a'+i）。
        // 为字典序最大，尽量把字符合并成更大的字母，即从高位到低位贪心。
        //
        // 位 25 及以上对应 'z'（因为 'a'+25 = 'z'，更高位无法再进位），
        // 位 0..24 分别对应 'a'..'y'。
        //
        // 构造规则：先输出所有能凑出的 'z'（即 x 的高于 24 位的部分），
        // 再按位从高到低，若该位为 1 就追加对应字母。

        let mut ans = Vec::with_capacity(nums.len());

        for x in nums {
            let x = x as u32;

            // 高位全部化为 'z'：每 2^25 个 'a' 可合并成一个 'z' 级别进位
            let z_count = x >> 25;
            let mut s = String::with_capacity(32);
            for _ in 0..z_count {
                s.push('z');
            }

            // 处理低 25 位，从高到低追加对应字母
            let max_bit = (24).min(31 - x.leading_zeros());
            for i in (0..=max_bit).rev() {
                if (x >> i) & 1 == 1 {
                    s.push((b'a' + i as u8) as char);
                }
            }

            ans.push(s);
        }

        ans
    }
}
```
