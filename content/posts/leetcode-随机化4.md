---
title: "Leetcode 随机化4"
date: 2024-12-21T15:03:43+08:00
tags: ["leetcode", "随机化"]
draft: false
---

## 打乱数组

给你一个整数数组 nums ，设计算法来打乱一个没有重复元素的数组。打乱后，数组的所有排列应该是 等可能 的。

实现 Solution class:

Solution(int[] nums) 使用整数数组 nums 初始化对象
int[] reset() 重设数组到它的初始状态并返回
int[] shuffle() 返回数组随机打乱后的结果

示例 1：

输入
["Solution", "shuffle", "reset", "shuffle"]
[[[1, 2, 3]], [], [], []]
输出
[null, [3, 1, 2], [1, 2, 3], [1, 3, 2]]

解释
Solution solution = new Solution([1, 2, 3]);
solution.shuffle();    // 打乱数组 [1,2,3] 并返回结果。任何 [1,2,3]的排列返回的概率应该相同。例如，返回 [3, 1, 2]
solution.reset();      // 重设数组到它的初始状态 [1, 2, 3] 。返回 [1, 2, 3]
solution.shuffle();    // 随机返回数组 [1, 2, 3] 打乱后的结果。例如，返回 [1, 3, 2]

```
struct Solution {
    nums: Vec<i32>,
    rng: rand::rngs::ThreadRng,
}

impl Solution {
    fn new(nums: Vec<i32>) -> Self {
        Self {
            nums,
            rng: rand::thread_rng(),
        }
    }

    fn reset(&self) -> Vec<i32> {
        self.nums.clone()
    }

    fn shuffle(&mut self) -> Vec<i32> {
        use rand::Rng;
        let len = self.nums.len();
        let mut shuffled = self.nums.clone();
        for i in 0..len {
            let  j = self.rng.gen_range(i..len);
            (shuffled[i], shuffled[j]) = (shuffled[j], shuffled[i]);
        }
        shuffled
    }
}
```