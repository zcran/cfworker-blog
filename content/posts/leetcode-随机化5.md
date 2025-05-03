---
title: "Leetcode 随机化5"
date: 2024-12-21T15:03:43+08:00
tags: ["leetcode", "随机化"]
draft: false
---

## 随机数索引

给你一个可能含有 重复元素 的整数数组 nums ，请你随机输出给定的目标数字 target 的索引。你可以假设给定的数字一定存在于数组中。

实现 Solution 类：

Solution(int[] nums) 用数组 nums 初始化对象。
int pick(int target) 从 nums 中选出一个满足 nums[i] == target 的随机索引 i 。如果存在多个有效的索引，则每个索引的返回概率应当相等。
 

示例：

输入
["Solution", "pick", "pick", "pick"]
[[[1, 2, 3, 3, 3]], [3], [1], [3]]
输出
[null, 4, 0, 2]

解释
Solution solution = new Solution([1, 2, 3, 3, 3]);
solution.pick(3); // 随机返回索引 2, 3 或者 4 之一。每个索引的返回概率应该相等。
solution.pick(1); // 返回 0 。因为只有 nums[0] 等于 1 。
solution.pick(3); // 随机返回索引 2, 3 或者 4 之一。每个索引的返回概率应该相等。

```
use rand::{rngs::ThreadRng, Rng};
use std::collections::HashMap;

struct Solution {
    map: HashMap<i32, Vec<usize>>,
    rand: ThreadRng,
}

impl Solution {
    fn new(nums: Vec<i32>) -> Self {
        Solution {
            map: nums
                .iter()
                .enumerate()
                .fold(HashMap::new(), |mut m, (i, &n)| {
                    m.entry(n).and_modify(|v| v.push(i)).or_insert(vec![i]);
                    m
                }),
            rand: rand::thread_rng(),
        }
    }

    fn pick(&mut self, target: i32) -> i32 {
        let v = self.map.get(&target).unwrap();
        let i = self.rand.gen_range(0..v.len());

        v[i] as i32
    }
}
```