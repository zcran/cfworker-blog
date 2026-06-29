---
title: "leetcode-单调栈27"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 找出最具竞争力的子序列


给你一个整数数组 nums 和一个正整数 k ，返回长度为 k 且最具 竞争力 的 nums 子序列。

数组的子序列是从数组中删除一些元素（可能不删除元素）得到的序列。

在子序列 a 和子序列 b 第一个不相同的位置上，如果 a 中的数字小于 b 中对应的数字，那么我们称子序列 a 比子序列 b（相同长度下）更具 竞争力 。 例如，[1,3,4] 比 [1,3,5] 更具竞争力，在第一个不相同的位置，也就是最后一个位置上， 4 小于 5 。

```
impl Solution {
    pub fn most_competitive(nums: Vec<i32>, k: i32) -> Vec<i32> {
        let k = k as usize;
        let n = nums.len();
        let mut stack = Vec::with_capacity(k);

        for (i, &num) in nums.iter().enumerate() {
            // 全部使用 usize 进行比较
            while let Some(&top) = stack.last()
                && num < top
                && stack.len() + (n - i) > k
            {
                stack.pop();
            }
            if stack.len() < k {
                stack.push(num);
            }
        }

        stack
    }
}
```
