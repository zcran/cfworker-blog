---
title: "leetcode-前缀和131"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 找出最小平衡下标

给你一个整数数组 nums。

如果某个下标 i 满足以下条件，则称其为 平衡下标： i 左侧所有元素的总和等于 i 右侧所有元素的乘积。

如果左侧没有元素，则总和视为 0。同样，如果右侧没有元素，则乘积视为 1。

要求返回最小的 平衡下标，如果不存在平衡下标，则返回 -1。


```
impl Solution {
    pub fn smallest_balanced_index(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 前缀和数组：sum[i] 表示 nums[0..i-1] 之和
        let mut sum = vec![0i64; n];
        for i in 0..n - 1 {
            sum[i + 1] = sum[i] + nums[i] as i64;
        }

        let mut mul = 1i64; // 后缀乘积：nums[i+1..n-1] 之积
        for i in (1..n).rev() {
            // 检查当前下标 i 是否平衡：左侧和 == 右侧积
            if sum[i] == mul {
                return i as i32;
            }

            // 剪枝：如果 mul > sum[i-1] / nums[i]，则继续向左遍历不可能找到答案
            // 因为向左时 mul 会乘以 nums[i]（非递减），而 sum 会越来越小
            let num = nums[i] as i64;
            if num != 0 && mul > sum[i - 1] / num {
                break;
            }
            mul *= num;
        }

        -1
    }
}
```
