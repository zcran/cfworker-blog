---
title: "leetcode-回溯67"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 下一个特殊回文数

给你一个整数 n。

如果一个数满足以下条件，那么它被称为 特殊数 ：

它是一个 回文数 。
数字中每个数字 k 出现 恰好 k 次。

返回 严格 大于 n 的 最小 特殊数。

如果一个整数正向读和反向读都相同，则它是 回文数 。例如，121 是回文数，而 123 不是。


```
impl Solution {
    pub fn special_palindrome(mut n: i64) -> i64 {
        // 题目要求严格大于 n，所以从 n+1 开始
        n += 1;

        // 记录每种位数下的最大可能值，用于确定结果位数
        // max_of_len[len] = 由数字 len 重复 len 次组成的数（该位数下的理论最大值）
        let mut max_of_len = vec![0];
        for digit in 1..10 {
            let mut val = 0;
            for _ in 0..digit {
                val = val * 10 + digit;
            }
            max_of_len.push(val);
        }
        // 手动补充一些超过9位的特殊数边界（用于确定更长位数）
        max_of_len.push(8888228888);
        max_of_len.push(99992929999);
        max_of_len.push(888822228888);
        max_of_len.push(9999449449999);
        max_of_len.push(88886666668888);
        max_of_len.push(999966696669999);
        max_of_len.push(8888666226668888);

        /// 获取数字 n 的位数，并根据最大可能值判断是否需要进位到更高位数
        fn get_num_len(num: i64, max_of_len: &[i64]) -> usize {
            let mut temp = num;
            let mut len = 0;
            while temp > 0 {
                len += 1;
                temp /= 10;
            }
            // 如果当前位数下无法容纳 num（超过该位数最大值），则位数+1
            if num > max_of_len[len] {
                len += 1;
            }
            len
        }

        // 确定目标特殊数的位数
        let target_len = get_num_len(n, &max_of_len);

        // 枚举所有可能的数字组合（每个数字 1..9 要么使用，要么不使用）
        let digits = vec![1, 2, 3, 4, 5, 6, 7, 8, 9];
        let mut used = vec![false; 10];
        let mut current_comb = Vec::new();
        let mut combinations = Vec::new();

        /// 深度优先搜索，枚举数字 1..9 的所有子集（每个数字最多出现一次）
        /// 收集所有总和等于 target_len 的子集
        fn dfs(
            digits: &[i32],
            current: &mut Vec<usize>,
            used: &mut Vec<bool>,
            mut sum: usize,
            combinations: &mut Vec<Vec<usize>>,
            target: usize,
            start: usize,
        ) {
            if sum == target {
                // 检查奇数频率的数字个数（回文数中最多只能有一个奇数频率数字）
                let mut odd_count = 0;
                for &digit in current.iter() {
                    if digit % 2 == 1 {
                        odd_count += 1;
                    }
                    if odd_count > 1 {
                        return; // 超过一个奇数频率，无法构成回文
                    }
                }
                combinations.push(current.clone());
                return;
            }

            // 从 start 开始避免重复组合
            for i in start..digits.len() {
                let digit = digits[i] as usize;
                if used[digit] {
                    continue;
                }
                used[digit] = true;
                sum += digit;
                current.push(digit);
                dfs(digits, current, used, sum, combinations, target, i + 1);
                current.pop();
                sum -= digit;
                used[digit] = false;
            }
        }

        dfs(
            &digits,
            &mut current_comb,
            &mut used,
            0,
            &mut combinations,
            target_len,
            0,
        );

        // 对每种数字组合，生成所有可能的回文排列
        // res[0] = n（下界），res[1] = 当前找到的最小候选（初始为极大值）
        let mut result = vec![n, 8_000_000_000_000_000];

        /// 递归生成回文数的左半部分所有排列
        /// src: 可用的数字列表（每个数字出现的次数为一半）
        /// count: 每个数字剩余可用次数
        /// current: 当前构造的左半部分
        /// odd_digit: 中间数字（如果有奇数频率数字），0 表示没有
        /// total_len: 回文数总长度
        /// result: 存储 [下界, 当前最小候选]
        fn generate_palindromes(
            src: &[usize],
            count: &mut Vec<usize>,
            current: &mut Vec<usize>,
            odd_digit: usize,
            total_len: usize,
            result: &mut Vec<i64>,
        ) {
            // 当左半部分构造完成，生成完整回文数
            if current.len() == total_len / 2 {
                let mut value = 0;

                // 计算从最高位开始的权重
                let mut power = 10_i64.pow((total_len - 1) as u32);

                // 左半部分（从高位到中间）
                for &digit in current.iter() {
                    value += power * digit as i64;
                    power /= 10;
                }

                // 中间数字（如果有）
                if odd_digit > 0 {
                    value += power * odd_digit as i64;
                    power /= 10;
                }

                // 右半部分（镜像左半部分）
                for &digit in current.iter().rev() {
                    value += power * digit as i64;
                    power /= 10;
                }

                // 只保留大于等于 n 的候选，并更新最小值
                if value >= result[0] && value < result[1] {
                    result[1] = value;
                }
                return;
            }

            // 从可用数字中选择下一个
            for i in 0..count.len() {
                if count[i] <= 0 {
                    continue;
                }
                count[i] -= 1;
                current.push(src[i]);
                generate_palindromes(src, count, current, odd_digit, total_len, result);
                current.pop();
                count[i] += 1;
            }
        }

        // 遍历所有数字组合，构造回文数
        for comb in combinations {
            let mut half_count = Vec::new();
            let mut odd_digit = 0;

            // 每个数字在回文数中出现 total_len 次，左半部分只需要一半
            for &digit in &comb {
                half_count.push(digit / 2);
                if digit % 2 == 1 {
                    odd_digit = digit;
                }
            }

            generate_palindromes(
                &comb,
                &mut half_count,
                &mut Vec::new(),
                odd_digit,
                target_len,
                &mut result,
            );
        }

        // 返回找到的最小候选
        result[1]
    }
}
```
