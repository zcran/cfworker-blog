---
title: "leetcode-双指针5"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 四数之和

给你一个由 n 个整数组成的数组 nums ，和一个目标值 target 。请你找出并返回满足下述全部条件且不重复的四元组 [nums[a], nums[b], nums[c], nums[d]] （若两个四元组元素一一对应，则认为两个四元组重复）：

0 <= a, b, c, d < n
a、b、c 和 d 互不相同
nums[a] + nums[b] + nums[c] + nums[d] == target

你可以按 任意顺序 返回答案 。


```
impl Solution {
    /// 四数之和（排序 + 双指针）
    /// 时间复杂度 O(n³)，空间复杂度 O(1)（不计返回结果）
    /// 在固定两个数后，使用双指针在剩余区间查找
    pub fn four_sum(mut nums: Vec<i32>, target: i32) -> Vec<Vec<i32>> {
        let n = nums.len();
        if n < 4 {
            return Vec::new();
        }

        nums.sort_unstable();
        let target = target as i64;
        let mut result = Vec::new();

        for i in 0..n - 3 {
            // 跳过重复的第一个数
            if i > 0 && nums[i] == nums[i - 1] {
                continue;
            }

            for j in i + 1..n - 2 {
                // 跳过重复的第二个数
                if j > i + 1 && nums[j] == nums[j - 1] {
                    continue;
                }

                let mut left = j + 1;
                let mut right = n - 1;

                while left < right {
                    let sum = nums[i] as i64 + nums[j] as i64 + nums[left] as i64 + nums[right] as i64;

                    if sum < target {
                        left += 1;
                    } else if sum > target {
                        right -= 1;
                    } else {
                        // 找到一组解
                        result.push(vec![nums[i], nums[j], nums[left], nums[right]]);

                        // 跳过重复的左指针元素
                        left += 1;
                        while left < right && nums[left] == nums[left - 1] {
                            left += 1;
                        }

                        // 跳过重复的右指针元素
                        right -= 1;
                        while left < right && nums[right] == nums[right + 1] {
                            right -= 1;
                        }
                    }
                }
            }
        }

        result
    }
}
```
