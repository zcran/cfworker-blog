---
title: "leetcode-单调队列11"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 最长递增子序列 II

给你一个整数数组 nums 和一个整数 k 。

找到 nums 中满足以下要求的最长子序列：

子序列 严格递增
子序列中相邻元素的差值 不超过 k 。
请你返回满足上述要求的 最长子序列 的长度。

子序列 是从一个数组中删除部分元素后，剩余元素不改变顺序得到的数组。

```
impl Solution {
    pub fn length_of_lis(nums: Vec<i32>, k: i32) -> i32 {
        const MAX_RANGE: usize = 100_001;
        let mut tree = vec![0; MAX_RANGE * 4];

        // 闭包：更新单点
        fn update(tree: &mut [i32], node: usize, l: usize, r: usize, pos: usize, val: i32) {
            if l == r {
                tree[node] = tree[node].max(val);
                return;
            }
            let mid = (l + r) / 2;
            if pos <= mid {
                update(tree, node * 2, l, mid, pos, val);
            } else {
                update(tree, node * 2 + 1, mid + 1, r, pos, val);
            }
            tree[node] = tree[node * 2].max(tree[node * 2 + 1]);
        }

        // 闭包：查询区间最大值
        fn query(tree: &[i32], node: usize, l: usize, r: usize, ql: usize, qr: usize) -> i32 {
            if ql > r || qr < l {
                return 0;
            }
            if ql <= l && r <= qr {
                return tree[node];
            }
            let mid = (l + r) / 2;
            let left = query(tree, node * 2, l, mid, ql, qr);
            let right = query(tree, node * 2 + 1, mid + 1, r, ql, qr);
            left.max(right)
        }

        let mut max_len = 0;
        for &num in &nums {
            let num = num as usize;
            let left = (num as i32 - k).max(0) as usize;
            let prev_max = if left <= num {
                query(&tree, 1, 0, MAX_RANGE - 1, left, num - 1)
            } else {
                0
            };
            let cur = prev_max + 1;
            update(&mut tree, 1, 0, MAX_RANGE - 1, num, cur);
            max_len = max_len.max(cur);
        }
        max_len
    }
}
```
