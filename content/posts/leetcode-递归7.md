---
title: "leetcode-递归7"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 排列序列


给出集合 [1,2,3,...,n]，其所有元素共有 n! 种排列。

按大小顺序列出所有排列情况，并一一标记，当 n = 3 时, 所有排列如下：

1."123"
2."132"
3."213"
4."231"
5."312"
6."321"

给定 n 和 k，返回第 k 个排列。


```
impl Solution {
    /// 第 k 个排列（逆康托展开详解）
    ///
    /// # 核心思想
    /// 排列与阶乘进制数一一对应：
    /// 第 k 个排列 = 将 k-1 表示为阶乘进制数，每位数字表示选剩余数字的第几个
    ///
    /// # 示例演示（n=4, k=9）
    /// 1. k' = 9-1 = 8
    /// 2. 8 = 1×3! + 1×2! + 0×1! = 1,1,0
    /// 3. 候选：[1,2,3,4]
    ///    - 第一位：选索引1 → [2], 剩余：[1,3,4]
    ///    - 第二位：选索引1 → [3], 剩余：[1,4]
    ///    - 第三位：选索引0 → [1], 剩余：[4]
    ///    - 第四位：选索引0 → [4]
    /// 4. 结果：2,3,1,4
    ///
    /// # 时间复杂度
    /// O(n²) - remove 操作需要 O(n)
    ///
    /// # 空间复杂度
    /// O(n) - 存储数字数组
    pub fn get_permutation(n: i32, k: i32) -> String {
        let n = n as usize;
        let mut k = k as usize - 1;

        // 1. 初始化候选数字
        let mut nums: Vec<char> = (1..=n).map(|i| (i as u8 + b'0') as char).collect();

        // 2. 预计算阶乘
        let mut fact = vec![1; n];
        for i in 1..n {
            fact[i] = fact[i - 1] * i;
        }

        // 3. 逆康托展开
        let mut result = String::with_capacity(n);
        for i in (0..n).rev() {
            let idx = k / fact[i];  // 确定当前位选第几个数字
            result.push(nums.remove(idx));
            k %= fact[i];            // 更新 k 为余数
        }

        result
    }
}
```
