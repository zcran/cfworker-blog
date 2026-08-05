---
title: "leetcode-滑动窗口108"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 使 K 个子数组内元素相等的最少操作数

给你一个整数数组 nums 和两个整数 x 和 k。你可以执行以下操作任意次（包括零次）：

将 nums 中的任意一个元素加 1 或减 1。

返回为了使 nums 中 至少 包含 k 个长度 恰好 为 x 的不重叠子数组（每个子数组中的所有元素都相等）所需要的 最少 操作数。

子数组 是数组中连续、非空的一段元素。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;
use std::collections::HashMap;

/// 懒删除最大堆（存储 i32）
struct LazyHeap {
    heap: BinaryHeap<i32>,
    remove_cnt: HashMap<i32, i32>,
    size: usize,
    sum: i64,
}

impl LazyHeap {
    fn new() -> Self {
        Self {
            heap: BinaryHeap::new(),
            remove_cnt: HashMap::new(),
            size: 0,
            sum: 0,
        }
    }

    fn apply_remove(&mut self) {
        while let Some(&top) = self.heap.peek() {
            if let Some(cnt) = self.remove_cnt.get_mut(&top) {
                if *cnt > 0 {
                    *cnt -= 1;
                    self.heap.pop();
                } else {
                    break;
                }
            } else {
                break;
            }
        }
    }

    fn size(&self) -> usize { self.size }
    fn sum(&self) -> i64 { self.sum }

    fn remove(&mut self, x: i32) {
        *self.remove_cnt.entry(x).or_insert(0) += 1;
        self.size -= 1;
        self.sum -= x as i64;
    }

    fn top(&mut self) -> i32 {
        self.apply_remove();
        *self.heap.peek().unwrap()
    }

    fn pop(&mut self) -> i32 {
        self.apply_remove();
        let x = self.heap.pop().unwrap();
        self.size -= 1;
        self.sum -= x as i64;
        x
    }

    fn push(&mut self, x: i32) {
        if let Some(cnt) = self.remove_cnt.get_mut(&x) {
            if *cnt > 0 {
                *cnt -= 1;
            } else {
                self.heap.push(x);
            }
        } else {
            self.heap.push(x);
        }
        self.size += 1;
        self.sum += x as i64;
    }

    fn push_pop(&mut self, x: i32) -> i32 {
        self.apply_remove();
        self.heap.push(x);
        self.sum += x as i64;
        let top = self.heap.pop().unwrap();
        self.sum -= top as i64;
        top
    }
}

/// 懒删除最小堆（存储 Reverse<i32>）
struct LazyHeapReverse {
    heap: BinaryHeap<Reverse<i32>>,
    remove_cnt: HashMap<Reverse<i32>, i32>,
    size: usize,
    sum: i64,
}

impl LazyHeapReverse {
    fn new() -> Self {
        Self {
            heap: BinaryHeap::new(),
            remove_cnt: HashMap::new(),
            size: 0,
            sum: 0,
        }
    }

    fn apply_remove(&mut self) {
        while let Some(&top) = self.heap.peek() {
            if let Some(cnt) = self.remove_cnt.get_mut(&top) {
                if *cnt > 0 {
                    *cnt -= 1;
                    self.heap.pop();
                } else {
                    break;
                }
            } else {
                break;
            }
        }
    }

    fn size(&self) -> usize { self.size }
    fn sum(&self) -> i64 { self.sum }

    fn remove(&mut self, x: Reverse<i32>) {
        *self.remove_cnt.entry(x).or_insert(0) += 1;
        self.size -= 1;
        self.sum -= x.0 as i64;
    }

    fn top(&mut self) -> Reverse<i32> {
        self.apply_remove();
        *self.heap.peek().unwrap()
    }

    fn pop(&mut self) -> Reverse<i32> {
        self.apply_remove();
        let x = self.heap.pop().unwrap();
        self.size -= 1;
        self.sum -= x.0 as i64;
        x
    }

    fn push(&mut self, x: Reverse<i32>) {
        if let Some(cnt) = self.remove_cnt.get_mut(&x) {
            if *cnt > 0 {
                *cnt -= 1;
            } else {
                self.heap.push(x);
            }
        } else {
            self.heap.push(x);
        }
        self.size += 1;
        self.sum += x.0 as i64;
    }

    fn push_pop(&mut self, x: Reverse<i32>) -> Reverse<i32> {
        self.apply_remove();
        self.heap.push(x);
        self.sum += x.0 as i64;
        let top = self.heap.pop().unwrap();
        self.sum -= top.0 as i64;
        top
    }
}

impl Solution {
    pub fn min_operations(nums: Vec<i32>, x: i32, k: i32) -> i64 {
        let n = nums.len();
        let x = x as usize;
        let k = k as usize;

        // 计算所有长度为 x 的子数组到其中位数的距离和
        let dis = Self::median_sliding_window(&nums, x);

        // dp[i][j]：前 j 个元素中选取 i 个不重叠子数组的最小代价
        let mut dp = vec![vec![i64::MAX; n + 1]; k + 1];
        // 不选任何子数组时，任意前缀代价为 0
        for j in 0..=n {
            dp[0][j] = 0;
        }

        for i in 1..=k {
            // j 的范围：至少 i*x，且右边留给剩余子数组足够的空间
            for j in i * x..=n - (k - i) * x {
                // 不选以 j 结尾的子数组，或选一个以 j-x 为起点的子数组
                dp[i][j] = dp[i][j - 1].min(
                    dp[i - 1][j - x].saturating_add(dis[j - x])
                );
            }
        }

        dp[k][n]
    }

    /// 滑动窗口 + 对顶堆计算每个长度为 x 的子数组到中位数的距离和
    fn median_sliding_window(nums: &[i32], x: usize) -> Vec<i64> {
        let n = nums.len();
        let mut ans = vec![0; n - x + 1];

        let mut left = LazyHeap::new();          // 最大堆（较小的一半）
        let mut right = LazyHeapReverse::new();  // 最小堆（较大的一半）

        for i in 0..n {
            let val = nums[i];

            // 1. 插入当前元素，维持两个堆的平衡
            if left.size() == right.size() {
                let popped = right.push_pop(Reverse(val));
                left.push(popped.0);
            } else {
                let popped = left.push_pop(val);
                right.push(Reverse(popped));
            }

            // 窗口未满则跳过
            if i + 1 < x {
                continue;
            }
            let window_start = i + 1 - x;

            // 2. 计算当前窗口的代价
            let median = left.top() as i64;
            let s1 = median * left.size() as i64 - left.sum();
            let s2 = right.sum() - median * right.size() as i64;
            ans[window_start] = s1 + s2;

            // 3. 移除离开窗口的元素
            let out = nums[window_start];
            if out <= left.top() {
                left.remove(out);
                // 平衡堆大小
                if left.size() < right.size() {
                    let moved = right.pop().0;
                    left.push(moved);
                }
            } else {
                right.remove(Reverse(out));
                if left.size() > right.size() + 1 {
                    let moved = left.pop();
                    right.push(Reverse(moved));
                }
            }
        }

        ans
    }
}
```
