---
title: "leetcode-树状树组3"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 计算右侧小于当前元素的个数

给你一个整数数组 nums ，按要求返回一个新数组 counts 。数组 counts 有该性质： counts[i] 的值是  nums[i] 右侧小于 nums[i] 的元素的数量。

```
impl Solution {
    /// 计算每个元素右侧比它小的元素个数
    /// 算法：从右向左扫描，维护一个有序的“右侧元素列表”
    ///       当前元素在该列表中的插入位置即为其右侧比它小的元素个数
    pub fn count_smaller(nums: Vec<i32>) -> Vec<i32> {
        nums.iter()
            .rev()                     // 从右向左扫描
            .fold(
                // 初始状态：(有序列表, 结果列表) 预分配容量，避免重复扩容
                (Vec::with_capacity(nums.len()), Vec::with_capacity(nums.len())),
                |(mut sorted, mut counts), &num| {
                    // 二分查找第一个 >= num 的位置 → 即为比 num 小的元素个数
                    let pos = sorted.partition_point(|&x| x < num);
                    sorted.insert(pos, num);   // 将当前元素插入有序列表
                    counts.push(pos as i32);   // 记录结果（逆序）
                    (sorted, counts)
                },
            )
            .1                               // 取出结果列表
            .into_iter()
            .rev()                           // 反转回原数组顺序
            .collect()
    }
}
```
