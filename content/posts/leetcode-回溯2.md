---
title: "leetcode-回溯2"
date: 2026-07-04T10:22:01+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 括号生成


数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。

```
impl Solution {
    /// 生成所有有效的括号组合
    ///
    /// # 示例
    /// ```
    /// 输入: n = 3
    /// 输出: ["((()))", "(()())", "(())()", "()(())", "()()()"]
    /// ```
    ///
    /// # 算法
    /// 使用回溯法，保证每一步都满足：
    /// - 左括号数量不超过 n
    /// - 右括号数量不超过左括号数量
    ///
    /// # 复杂度
    /// - 时间复杂度: O(4^n / √n)，卡特兰数级别
    /// - 空间复杂度: O(n)，递归栈深度
    pub fn generate_parenthesis(n: i32) -> Vec<String> {
        let n = n as usize;
        let mut result = Vec::new();

        // 预分配容量优化性能
        let capacity = Self::catalan(n);
        result.reserve(capacity);

        let mut current = String::with_capacity(n * 2);
        Self::backtrack(&mut current, n, 0, 0, &mut result);
        result
    }

    /// 回溯生成括号组合
    ///
    /// # 参数
    /// - `current`: 当前构建的字符串
    /// - `n`: 括号对数
    /// - `left`: 已使用的左括号数
    /// - `right`: 已使用的右括号数
    /// - `result`: 存储所有有效组合
    fn backtrack(
        current: &mut String,
        n: usize,
        left: usize,
        right: usize,
        result: &mut Vec<String>,
    ) {
        // 达到目标长度，保存结果
        if current.len() == 2 * n {
            result.push(current.clone());
            return;
        }

        // 尝试添加左括号：左括号数量不能超过 n
        if left < n {
            current.push('(');
            Self::backtrack(current, n, left + 1, right, result);
            current.pop(); // 回溯
        }

        // 尝试添加右括号：右括号数量必须小于左括号数量（保证有效性）
        if right < left {
            current.push(')');
            Self::backtrack(current, n, left, right + 1, result);
            current.pop(); // 回溯
        }
    }

    /// 计算卡特兰数，用于预分配容量
    ///
    /// 卡特兰数 C_n = (2n)! / (n! * (n+1)!)
    /// 表示所有可能的有效括号组合数量
    #[inline]
    fn catalan(n: usize) -> usize {
        if n <= 1 {
            return 1;
        }

        // 使用递推公式: C_n = C_{n-1} * 2 * (2n - 1) / (n + 1)
        let mut c = 1;
        for i in 1..=n {
            c = c * 2 * (2 * i - 1) / (i + 1);
        }
        c
    }
}
```
