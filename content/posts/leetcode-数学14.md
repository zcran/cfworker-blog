---
title: "leetcode-数学14"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 负二进制数相加

给出基数为 -2 的两个数 arr1 和 arr2，返回两数相加的结果。

数字以 数组形式 给出：数组由若干 0 和 1 组成，按最高有效位到最低有效位的顺序排列。例如，arr = [1,1,0,1] 表示数字 (-2)^3 + (-2)^2 + (-2)^0 = -3。数组形式 中的数字 arr 也同样不含前导零：即 arr == [0] 或 arr[0] == 1。

返回相同表示形式的 arr1 和 arr2 相加的结果。两数的表示形式为：不含前导零、由若干 0 和 1 组成的数组。


```
impl Solution {
    /// 将两个负二进制（base -2）数组表示的数相加，返回相同表示形式的结果。
    ///
    /// 核心思路：从最低位（数组末尾）开始逐位相加，并处理进位。
    /// 负二进制与二进制加法的区别在于进位方向：
    ///   - 当前位的和 sum = a + b + carry。
    ///   - 该位结果 = sum & 1（取最低位）。
    ///   - 新的进位 carry = -(sum >> 1)（因为 (-2) 的进位是负的）。
    ///
    /// 例如 sum = 2 → 该位 0，进位 -1；sum = 3 → 该位 1，进位 -1。
    /// 如此循环直到两数都处理完且进位为 0。
    /// 最后去掉前导零；若全为零则返回 [0]。
    pub fn add_negabinary(arr1: Vec<i32>, arr2: Vec<i32>) -> Vec<i32> {
        let (m, n) = (arr1.len(), arr2.len());
        let (mut i, mut j) = (m as isize - 1, n as isize - 1);
        let mut carry = 0;
        let mut res = Vec::with_capacity(m.max(n) + 2);

        // 从低位到高位相加，结果低位先入栈
        while i >= 0 || j >= 0 || carry != 0 {
            let a = if i >= 0 { arr1[i as usize] } else { 0 };
            let b = if j >= 0 { arr2[j as usize] } else { 0 };
            let sum = a + b + carry;

            res.push(sum & 1);
            carry = -(sum >> 1);

            i -= 1;
            j -= 1;
        }

        // 去掉高位多余的 0（当前 res 低位在前，末尾是高位）
        while res.len() > 1 && *res.last().unwrap() == 0 {
            res.pop();
        }

        // 反转成高位在前的表示形式
        res.reverse();
        res
    }
}
```
