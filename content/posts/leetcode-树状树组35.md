---
title: "leetcode-树状树组35"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 数组中的峰值

数组 arr 中 大于 前面和后面相邻元素的元素被称为 峰值 元素。

给你一个整数数组 nums 和一个二维整数数组 queries 。

你需要处理以下两种类型的操作：

queries[i] = [1, li, ri] ，求出子数组 nums[li..ri] 中 峰值 元素的数目。
queries[i] = [2, indexi, vali] ，将 nums[indexi] 变为 vali 。
请你返回一个数组 answer ，它依次包含每一个第一种操作的答案。

注意：

子数组中 第一个 和 最后一个 元素都 不是 峰值元素。

```
/// 树状数组（Fenwick Tree），支持单点增加和前缀和查询
struct Fenwick {
    tree: Vec<i32>,
}

impl Fenwick {
    /// 创建一个大小为 n 的树状数组（索引范围 1..=n）
    fn new(n: usize) -> Self {
        Self { tree: vec![0; n + 1] }
    }

    #[inline]
    fn lowbit(x: usize) -> usize {
        x & x.wrapping_neg()
    }

    /// 在位置 i（1‑based）增加 val
    fn add(&mut self, mut i: usize, val: i32) {
        while i < self.tree.len() {
            self.tree[i] += val;
            i += Self::lowbit(i);
        }
    }

    /// 前缀和 [1, i]
    fn prefix(&self, mut i: usize) -> i32 {
        let mut sum = 0;
        while i > 0 {
            sum += self.tree[i];
            i &= i - 1;
        }
        sum
    }

    /// 区间和 [l, r]（闭区间，1‑based）
    fn range_sum(&self, l: usize, r: usize) -> i32 {
        if r < l {
            0
        } else {
            self.prefix(r) - self.prefix(l - 1)
        }
    }
}

impl Solution {
    pub fn count_of_peaks(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = nums.len();
        // 数组长度不足 3 时不可能有峰值，所有查询结果均为 0
        if n < 3 {
            return vec![0; queries.iter().filter(|q| q[0] == 1).count()];
        }

        let mut bit = Fenwick::new(n - 2); // 树状数组维护位置 1..=n-2（原数组索引）
        let mut heights = nums;

        // 判断位置 i（1‑based）是否为峰值（要求 i 在 [1, n-2] 内）
        fn is_peak(heights: &[i32], i: usize) -> bool {
            heights[i - 1] < heights[i] && heights[i] > heights[i + 1]
        }

        // 更新树状数组：若位置 i 是峰值，则增加 val（+1 或 -1）
        fn update_peak(bit: &mut Fenwick, heights: &[i32], i: usize, val: i32) {
            if is_peak(heights, i) {
                bit.add(i, val);
            }
        }

        // 初始化树状数组，将所有峰值位置标记
        for i in 1..=n - 2 {
            update_peak(&mut bit, &heights, i, 1);
        }

        let mut answers = Vec::new();

        for q in queries {
            match q[0] {
                1 => {
                    // 查询子数组 nums[l..=r] 中的峰值个数（l, r 为 0‑based）
                    let l = q[1] as usize;
                    let r = q[2] as usize;
                    // 峰值必须位于 [l+1, r-1]（0‑based），转换为树状数组下标（1‑based）
                    let left = (l + 1).max(1);                    // 左边界，至少为 1
                    let right = if r >= 1 { (r - 1).min(n - 2) } else { 0 }; // 右边界，防止下溢
                    let cnt = if left <= right {
                        bit.range_sum(left, right)
                    } else {
                        0
                    };
                    answers.push(cnt);
                }
                2 => {
                    // 修改单个元素 nums[index] = new_val
                    let index = q[1] as usize;
                    let new_val = q[2];
                    // 受影响的原数组下标范围（0‑based）
                    let lo = (index as i32 - 1).max(0) as usize;
                    let hi = (index + 1).min(n - 1);
                    // 先清除旧标记
                    for pos in lo..=hi {
                        if pos >= 1 && pos <= n - 2 {
                            update_peak(&mut bit, &heights, pos, -1);
                        }
                    }
                    // 更新数组值
                    heights[index] = new_val;
                    // 再添加新标记
                    for pos in lo..=hi {
                        if pos >= 1 && pos <= n - 2 {
                            update_peak(&mut bit, &heights, pos, 1);
                        }
                    }
                }
                _ => unreachable!(),
            }
        }

        answers
    }
}
```
