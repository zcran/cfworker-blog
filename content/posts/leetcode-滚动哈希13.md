---
title: "leetcode-滚动哈希13"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 含最多 K 个可整除元素的子数组

给你一个整数数组 nums 和两个整数 k 和 p ，找出并返回满足要求的不同的子数组数，要求子数组中最多 k 个可被 p 整除的元素。

如果满足下述条件之一，则认为数组 nums1 和 nums2 是 不同 数组：

两数组长度 不同 ，或者
存在 至少 一个下标 i 满足 nums1[i] != nums2[i] 。
子数组 定义为：数组中的连续元素组成的一个 非空 序列。


```
use std::collections::HashSet;

impl Solution {
    /// 计算所有子数组（连续）中，能被 p 整除的元素个数不超过 k 的**不同子数组**的数量。
    /// 不同子数组指元素序列不同，而非起始索引不同。
    ///
    /// # 优化点
    /// - 直接使用 `&[i32]` 切片引用作为哈希键，无需复制数据或构造字符串。
    /// - 内层循环提前终止，避免无效扩展。
    /// - 时间复杂度 O(n²)（最坏情况），但常数极小；空间复杂度 O(n²) 存储所有合法子数组的引用。
    pub fn count_distinct(nums: Vec<i32>, k: i32, p: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;          // 转为 usize 方便比较
        let mut set = HashSet::new();

        // 枚举所有起始位置
        for i in 0..n {
            let mut cnt = 0;         // 当前子数组中能被 p 整除的元素个数
            // 从 i 开始向右扩展子数组
            for j in i..n {
                if nums[j] % p == 0 {
                    cnt += 1;
                    if cnt > k {
                        break;       // 已超过限制，更长的子数组都不合法
                    }
                }
                // 插入子数组 nums[i..=j] 的切片引用（零拷贝）
                set.insert(&nums[i..=j]);
            }
        }

        set.len() as i32
    }
}
```
