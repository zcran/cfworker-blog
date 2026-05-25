---
title: "leetcode-树状树组15"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 统计数组中好三元组数目

给你两个下标从 0 开始且长度为 n 的整数数组 nums1 和 nums2 ，两者都是 [0, 1, ..., n - 1] 的 排列 。

好三元组 指的是 3 个 互不相同 的值，且它们在数组 nums1 和 nums2 中出现顺序保持一致。换句话说，如果我们将 pos1v 记为值 v 在 nums1 中出现的位置，pos2v 为值 v 在 nums2 中的位置，那么一个好三元组定义为 0 <= x, y, z <= n - 1 ，且 pos1x < pos1y < pos1z 和 pos2x < pos2y < pos2z 都成立的 (x, y, z) 。

请你返回好三元组的 总数目 。

```
/// 树状数组 (Fenwick Tree) 支持单点更新和前缀和查询。
struct FenwickTree {
    tree: Vec<i32>,
}

impl FenwickTree {
    /// 创建大小为 `size` 的树状数组（内部数组长度为 size+1，忽略下标 0）。
    fn new(size: usize) -> Self {
        FenwickTree {
            tree: vec![0; size + 1],
        }
    }

    /// 在位置 `index`（0‑based）上增加 `delta`。
    fn update(&mut self, index: usize, delta: i32) {
        let mut i = index + 1; // 转换为 1‑based 索引
        while i < self.tree.len() {
            self.tree[i] += delta;
            i += i & (!i + 1); // 加 lowbit
        }
    }

    /// 查询前缀和 `[0, index]`（包含），返回和。
    fn query(&self, index: usize) -> i32 {
        let mut i = index + 1;
        let mut sum = 0;
        while i > 0 {
            sum += self.tree[i];
            i -= i & (!i + 1); // 减 lowbit
        }
        sum
    }
}

impl Solution {
    /// 计算两个排列中的“好三元组”数量。
    /// 一个三元组 (a, b, c) 是好的，当且仅当它在 nums1 和 nums2 中都是递增的。
    ///
    /// 算法思路：
    /// 1. 对于每个值 x（0..n-1），记它在 nums1 中的位置为 `i`，在 nums2 中的位置为 `j`。
    /// 2. 将 `(j, i)` 作为键值对存储，并按 `j` 升序处理。
    /// 3. 当处理到某个 `j` 时，利用树状数组统计所有已处理的 `i` 中小于当前 `i` 的个数（即 `left`）。
    /// 4. 尚未处理的元素中，大于当前 `i` 的个数 `right` 可由公式计算：
    ///    `right = (n - 1 - i) - (已处理元素总数 - left)`
    ///    其中 `已处理元素总数 = j`（因为已经处理了前 `j` 个位置）。
    /// 5. 累加 `left * right` 到答案。
    pub fn good_triplets(nums1: Vec<i32>, nums2: Vec<i32>) -> i64 {
        let n = nums1.len();
        // 记录每个值在 nums2 中的位置（0‑based）
        let mut pos_in_nums2 = vec![0; n];
        for (j, &val) in nums2.iter().enumerate() {
            pos_in_nums2[val as usize] = j;
        }

        // 构建映射：对于每个值 x，通过 pos_in_nums2 得到它在 nums2 中的位置 j，
        // 然后记录这个 j 对应的 nums1 中的位置 i。
        // 注意：j 的范围是 0..n-1，因此可以按 j 作为下标存储 i。
        let mut index_in_nums1_for_j = vec![0; n];
        for (i, &val) in nums1.iter().enumerate() {
            let j = pos_in_nums2[val as usize];
            index_in_nums1_for_j[j] = i; // j → i
        }

        let mut bit = FenwickTree::new(n);
        let mut total_good_triplets = 0i64;

        // 按 nums2 的顺序（即 j 递增）处理每个位置
        for j in 0..n {
            let i = index_in_nums1_for_j[j];          // 当前值在 nums1 中的位置
            let left = bit.query(i) as i64;           // 已处理且 i' < i 的个数
            bit.update(i, 1);                         // 将当前 i 加入树状数组

            let processed = j as i64;                  // 已经处理了 j 个元素（j 从0开始）
            let right = (n - 1 - i) as i64 - (processed - left); // 未处理且 i' > i 的个数
            total_good_triplets += left * right;
        }

        total_good_triplets
    }
}
```
