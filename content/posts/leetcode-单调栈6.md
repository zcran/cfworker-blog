---
title: "leetcode-单调栈6"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 移掉 K 位数字


给你一个以字符串表示的非负整数 num 和一个整数 k ，移除这个数中的 k 位数字，使得剩下的数字最小。请你以字符串形式返回这个最小的数字。

```
impl Solution {
    /// 移除 k 个数字后得到最小可能的数
    ///
    /// 核心思路：使用单调递增栈，保证栈中元素尽可能小且保持原顺序
    pub fn remove_kdigits(num: String, k: i32) -> String {
        let mut k = k as usize;           // 剩余可删除次数
        let mut stack = Vec::with_capacity(num.len() - k); // 预分配容量，避免多次扩容

        // 贪心删除：遇到比栈顶小的元素时，弹出栈顶（删除更大的高位数字）
        for &digit in num.as_bytes() {
            // 当还能删除且栈非空且当前数字 < 栈顶时，弹出栈顶
            while k > 0 && !stack.is_empty() && *stack.last().unwrap() > digit {
                stack.pop();
                k -= 1;
            }
            stack.push(digit);
        }

        // 如果还有剩余删除次数，从末尾删除（此时栈已是递增，删除大的低位）
        stack.truncate(stack.len() - k);

        // 转换为字符串并去除前导零
        let result = String::from_utf8(stack)
            .unwrap()
            .trim_start_matches('0')
            .to_string();

        // 空字符串返回 "0"
        if result.is_empty() { "0".to_string() } else { result }
    }
}
```
