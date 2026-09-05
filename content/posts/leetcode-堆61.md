---
title: "leetcode-堆61"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 零数组变换 III

给你一个长度为 n 的整数数组 nums 和一个二维数组 queries ，其中 queries[i] = [li, ri] 。

每一个 queries[i] 表示对于 nums 的以下操作：

将 nums 中下标在范围 [li, ri] 之间的每一个元素 最多 减少 1 。
坐标范围内每一个元素减少的值相互 独立 。

零数组 指的是一个数组里所有元素都等于 0 。

请你返回 最多 可以从 queries 中删除多少个元素，使得 queries 中剩下的元素仍然能将 nums 变为一个 零数组 。如果无法将 nums 变为一个 零数组 ，返回 -1 。


```
use std::collections::BinaryHeap;

impl Solution {
    pub fn max_removal(nums: Vec<i32>, mut queries: Vec<Vec<i32>>) -> i32 {
        let n = nums.len();
        // 按左端点升序排列，方便从左到右扫描
        queries.sort_unstable_by_key(|q| q[0]);

        let mut heap = BinaryHeap::new(); // 大根堆：存候选查询的右端点
        let mut diff = vec![0i32; n + 1]; // 差分数组，记录覆盖范围的结束
        let mut cover = 0i32;             // 当前位置被多少个已选查询覆盖
        let mut q = 0usize;               // 查询数组指针

        for i in 0..n {
            cover += diff[i];

            // 把所有左端点 <= i 的查询加入候选池
            while q < queries.len() && queries[q][0] <= i as i32 {
                heap.push(queries[q][1]);
                q += 1;
            }

            // 覆盖次数不足 nums[i]，从候选池中选右端点最大的查询使用
            while cover < nums[i] {
                match heap.peek() {
                    // 右端点最大的查询能覆盖当前位置，使用它
                    Some(&r) if r >= i as i32 => {
                        cover += 1;
                        diff[(heap.pop().unwrap() + 1) as usize] -= 1;
                    }
                    // 堆顶都覆盖不了，说明没有可用查询
                    _ => return -1,
                }
            }
        }

        // 堆中剩余 = 总查询 - 已使用 = 最多可删除数
        // （过期查询虽未清理，但它们确实可被删除，不影响答案）
        heap.len() as i32
    }
}
```
