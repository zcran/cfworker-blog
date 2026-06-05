---
title: "leetcode-递归31"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 找出第 K 个字符 I

Alice 和 Bob 正在玩一个游戏。最初，Alice 有一个字符串 word = "a"。

给定一个正整数 k。

现在 Bob 会要求 Alice 执行以下操作 无限次 :

将 word 中的每个字符 更改 为英文字母表中的 下一个 字符来生成一个新字符串，并将其 追加 到原始的 word。

例如，对 "c" 进行操作生成 "cd"，对 "zb" 进行操作生成 "zbac"。

在执行足够多的操作后， word 中 至少 存在 k 个字符，此时返回 word 中第 k 个字符的值。


```
impl Solution {
    /// 查找第 k 个字符（LeetCode 3304 题）
    ///
    /// # 问题说明
    /// 字符串的构造规则：
    /// - word = "a"
    /// - 每次操作：将 word 中的每个字符替换为下一个字符（'a'->'b', 'b'->'c', ...）
    /// - 然后追加到原字符串后面
    ///
    /// 例如：
    /// - 第 0 次："a"
    /// - 第 1 次："a" + "b" = "ab"
    /// - 第 2 次："ab" + "bc" = "abbc"
    /// - 第 3 次："abbc" + "bccd" = "abbcbccd"
    ///
    /// # 算法思路
    /// 通过逆向推导，每次减去小于 k 的最大 2 的幂，累加变换次数，
    /// 最终得到字符相对于 'a' 的偏移量。
    ///
    /// 数学原理：
    /// 每次操作相当于将字符串长度翻倍，新增的部分是原字符串每个字符 +1
    /// 因此，第 k 个字符可以通过不断减去 2 的幂来追踪其"来源"
    ///
    /// # 复杂度
    /// - 时间复杂度：O(log k) - 每次循环 k 至少减半
    /// - 空间复杂度：O(1)
    ///
    /// # 示例
    /// ```
    /// assert_eq!(Solution::kth_character(1), 'a');
    /// assert_eq!(Solution::kth_character(2), 'b');
    /// assert_eq!(Solution::kth_character(3), 'b');
    /// assert_eq!(Solution::kth_character(4), 'c');
    /// assert_eq!(Solution::kth_character(5), 'b');
    /// ```
    pub fn kth_character(k: i32) -> char {
        let mut offset = 0;  // 字符偏移量，从 'a' 开始
        let mut remaining = k;  // 剩余位置

        while remaining > 1 {
            // 找到小于 remaining 的最大 2 的幂
            // 例如：remaining=5，最大幂是 4；remaining=4，最大幂是 2（因为要小于它）
            let power = Self::largest_power_of_two_less_than(remaining);

            // 减去这个幂，相当于跳转到前半部分的对应位置
            remaining -= power;

            // 每跳过一次变换，字符就增加 1
            offset += 1;
        }

        // 'a' 加上偏移量得到最终字符
        (b'a' + offset) as char
    }

    /// 找到小于 n 的最大 2 的幂
    ///
    /// # 参数
    /// - `n`: 正整数（n >= 2）
    ///
    /// # 返回
    /// - 小于 n 的最大 2 的幂
    ///
    /// # 示例
    /// ```
    /// assert_eq!(Solution::largest_power_of_two_less_than(2), 1);
    /// assert_eq!(Solution::largest_power_of_two_less_than(3), 2);
    /// assert_eq!(Solution::largest_power_of_two_less_than(4), 2);
    /// assert_eq!(Solution::largest_power_of_two_less_than(5), 4);
    /// assert_eq!(Solution::largest_power_of_two_less_than(8), 4);
    /// assert_eq!(Solution::largest_power_of_two_less_than(9), 8);
    /// ```
    #[inline]
    fn largest_power_of_two_less_than(n: i32) -> i32 {
        // 方法1：使用位运算（最快）
        // 1 << (31 - n.leading_zeros()) 得到小于等于 n 的最大 2 的幂
        // 但如果 n 本身是 2 的幂，需要再除以 2
        let highest_power = 1 << (31 - n.leading_zeros());
        if highest_power == n {
            highest_power >> 1
        } else {
            highest_power
        }
    }
}
```
