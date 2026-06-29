---
title: "leetcode-单调栈43"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 边界元素是最大值的子数组数目

给你一个 正 整数数组 nums 。

请你求出 nums 中有多少个子数组，满足子数组中 第一个 和 最后一个 元素都是这个子数组中的 最大 值。



```
impl Solution {
    /// 统计子数组：首尾都是最大值
    ///
    /// 单调栈维护递减序列，相同值合并计数
    /// 每个新元素与前面每个相同元素形成合法子数组
    pub fn number_of_subarrays(nums: Vec<i32>) -> i64 {
        let mut ans = 0i64;
        let mut stack = Vec::new();  // (值, 连续出现次数)

        for &x in &nums {
            let mut cnt = 1;
            // 弹出小于 x 的栈顶
            while let Some(&(v, _)) = stack.last() {
                if x > v {
                    stack.pop();
                } else {
                    break;
                }
            }
            // 与栈顶相等则合并
            if let Some(&(v, c)) = stack.last() {
                if x == v {
                    cnt += c;
                    ans += c;
                }
            }
            stack.push((x, cnt));
            ans += 1;  // 长度为1的子数组
        }

        ans
    }
}
```
