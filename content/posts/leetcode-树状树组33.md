---
title: "leetcode-树状树组33"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 将元素分配到两个数组中 II

给你一个下标从 1 开始、长度为 n 的整数数组 nums 。

现定义函数 greaterCount ，使得 greaterCount(arr, val) 返回数组 arr 中 严格大于 val 的元素数量。

你需要使用 n 次操作，将 nums 的所有元素分配到两个数组 arr1 和 arr2 中。在第一次操作中，将 nums[1] 追加到 arr1 。在第二次操作中，将 nums[2] 追加到 arr2 。之后，在第 i 次操作中：

·如果 greaterCount(arr1, nums[i]) > greaterCount(arr2, nums[i]) ，将 nums[i] 追加到 arr1 。
·如果 greaterCount(arr1, nums[i]) < greaterCount(arr2, nums[i]) ，将 nums[i] 追加到 arr2 。
·如果 greaterCount(arr1, nums[i]) == greaterCount(arr2, nums[i]) ，将 nums[i] 追加到元素数量较少的数组中。
·如果仍然相等，那么将 nums[i] 追加到 arr1 。

连接数组 arr1 和 arr2 形成数组 result 。例如，如果 arr1 == [1,2,3] 且 arr2 == [4,5,6] ，那么 result = [1,2,3,4,5,6] 。

返回整数数组 result 。

```
use std::collections::HashMap;

/// 树状数组（Binary Indexed Tree），支持单点增加和前缀和查询
struct BinaryIndexedTree {
    tree: Vec<i32>,
}

impl BinaryIndexedTree {
    /// 创建一个大小为 n 的树状数组（有效下标 1..=n）
    fn new(n: usize) -> Self {
        Self { tree: vec![0; n + 1] }
    }

    #[inline]
    fn lowbit(x: usize) -> usize {
        x & x.wrapping_neg()
    }

    /// 在位置 i (1‑based) 增加 1
    fn add(&mut self, mut i: usize) {
        while i < self.tree.len() {
            self.tree[i] += 1;
            i += Self::lowbit(i);
        }
    }

    /// 查询前缀和 [1, i]
    fn prefix_sum(&self, mut i: usize) -> i32 {
        let mut sum = 0;
        while i > 0 {
            sum += self.tree[i];
            i -= Self::lowbit(i);
        }
        sum
    }
}

impl Solution {
    pub fn result_array(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        if n <= 2 {
            return nums;
        }

        // ----- 1. 离散化（去重，保证排名连续）-----
        let mut sorted = nums.clone();
        sorted.sort_unstable();
        sorted.dedup(); // 去重，得到所有不同值
        let rank_of: HashMap<i32, usize> = sorted
            .iter()
            .enumerate()
            .map(|(idx, &val)| (val, idx + 1))
            .collect();

        let m = sorted.len(); // 不同值的个数

        // ----- 2. 初始化两个数组与树状数组 -----
        let mut arr1 = vec![nums[0]];
        let mut arr2 = vec![nums[1]];
        let mut bit1 = BinaryIndexedTree::new(m);
        let mut bit2 = BinaryIndexedTree::new(m);

        bit1.add(rank_of[&nums[0]]);
        bit2.add(rank_of[&nums[1]]);

        // 辅助函数：统计当前数组中严格大于当前值的元素个数
        let greater_count = |arr_len: usize, bit: &BinaryIndexedTree, rank: usize| -> i32 {
            arr_len as i32 - bit.prefix_sum(rank)
        };

        // ----- 3. 处理剩余元素，决定放入 arr1 还是 arr2 -----
        for i in 2..n {
            let x = nums[i];
            let r = rank_of[&x]; // 1..=m，安全

            let cnt1 = greater_count(arr1.len(), &bit1, r);
            let cnt2 = greater_count(arr2.len(), &bit2, r);

            if cnt1 > cnt2 || (cnt1 == cnt2 && arr1.len() <= arr2.len()) {
                arr1.push(x);
                bit1.add(r);
            } else {
                arr2.push(x);
                bit2.add(r);
            }
        }

        // ----- 4. 合并结果 -----
        arr1.extend(arr2);
        arr1
    }
}
```
