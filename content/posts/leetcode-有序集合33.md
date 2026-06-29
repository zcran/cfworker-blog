---
title: "leetcode-有序集合33"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 水果成篮 III

给你两个长度为 n 的整数数组，fruits 和 baskets，其中 fruits[i] 表示第 i 种水果的 数量，baskets[j] 表示第 j 个篮子的 容量。

你需要对 fruits 数组从左到右按照以下规则放置水果：

· 每种水果必须放入第一个 容量大于等于 该水果数量的 最左侧可用篮子 中。
· 每个篮子只能装 一种 水果。
· 如果一种水果 无法放入 任何篮子，它将保持 未放置。

返回所有可能分配完成后，剩余未放置的水果种类的数量。


```
impl Solution {
    pub fn num_of_unplaced_fruits(fruits: Vec<i32>, baskets: Vec<i32>) -> i32 {
        let n = baskets.len();
        let mut seg = vec![0; 4 * n];
        build(&mut seg, &baskets, 1, 0, n - 1);

        let mut unplaced = 0;
        for &fruit in &fruits {
            let pos = query(&mut seg, 1, 0, n - 1, fruit);
            if pos == -1 {
                unplaced += 1;
            } else {
                update(&mut seg, 1, 0, n - 1, pos as usize, -1);
            }
        }

        unplaced
    }
}

fn build(seg: &mut Vec<i32>, baskets: &[i32], node: usize, l: usize, r: usize) {
    if l == r {
        seg[node] = baskets[l];
        return;
    }
    let mid = (l + r) / 2;
    build(seg, baskets, node * 2, l, mid);
    build(seg, baskets, node * 2 + 1, mid + 1, r);
    seg[node] = seg[node * 2].max(seg[node * 2 + 1]);
}

fn query(seg: &mut Vec<i32>, node: usize, l: usize, r: usize, fruit: i32) -> i32 {
    if seg[node] < fruit {
        return -1;
    }
    if l == r {
        return l as i32;
    }
    let mid = (l + r) / 2;
    // 优先查找左子树（最左侧）
    if seg[node * 2] >= fruit {
        query(seg, node * 2, l, mid, fruit)
    } else {
        query(seg, node * 2 + 1, mid + 1, r, fruit)
    }
}

fn update(seg: &mut Vec<i32>, node: usize, l: usize, r: usize, pos: usize, val: i32) {
    if l == r {
        seg[node] = val;
        return;
    }
    let mid = (l + r) / 2;
    if pos <= mid {
        update(seg, node * 2, l, mid, pos, val);
    } else {
        update(seg, node * 2 + 1, mid + 1, r, pos, val);
    }
    seg[node] = seg[node * 2].max(seg[node * 2 + 1]);
}
```
