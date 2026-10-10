---
title: "leetcode-哈希表7"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 随机数索引

给你一个可能含有 重复元素 的整数数组 nums ，请你随机输出给定的目标数字 target 的索引。你可以假设给定的数字一定存在于数组中。

实现 Solution 类：

Solution(int[] nums) 用数组 nums 初始化对象。
int pick(int target) 从 nums 中选出一个满足 nums[i] == target 的随机索引 i 。如果存在多个有效的索引，则每个索引的返回概率应当相等。



```
use rand::Rng;
use std::collections::HashMap;

struct Solution {
    // 预处理：把每个值对应的所有索引存起来，pick 时随机取一个即可。
    // 相比蓄水池抽样每次 O(n) 遍历，这里查询为 O(1)（随机取下标）。
    map: HashMap<i32, Vec<usize>>,
}

impl Solution {
    fn new(nums: Vec<i32>) -> Self {
        let mut map: HashMap<i32, Vec<usize>> = HashMap::new();
        for (i, n) in nums.into_iter().enumerate() {
            map.entry(n).or_default().push(i);
        }
        Self { map }
    }

    fn pick(&self, target: i32) -> i32 {
        let v = &self.map[&target];
        // 索引均匀随机，每个位置被选中的概率相等。
        v[rand::thread_rng().gen_range(0..v.len())] as i32
    }
}
```
