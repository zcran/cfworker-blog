---
title: "leetcode-位掩码29"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 将石头分散到网格图的最少移动次数


给你一个大小为 3 * 3 ，下标从 0 开始的二维整数矩阵 grid ，分别表示每一个格子里石头的数目。网格图中总共恰好有 9 个石头，一个格子里可能会有 多个 石头。

每一次操作中，你可以将一个石头从它当前所在格子移动到一个至少有一条公共边的相邻格子。

请你返回每个格子恰好有一个石头的 最少移动次数 。


```
impl Solution {
    /// 计算将多余石子移动到空格的最小曼哈顿距离总和
    pub fn minimum_moves(grid: Vec<Vec<i32>>) -> i32 {
        // 1. 收集多余石子位置（需要移动）和空位位置
        let mut more = Vec::new();
        let mut less = Vec::new();
        for i in 0..3 {
            for j in 0..3 {
                match grid[i][j] {
                    v if v > 1 => {
                        for _ in 0..v - 1 {
                            more.push((i as i32, j as i32));
                        }
                    }
                    0 => less.push((i as i32, j as i32)),
                    _ => {}
                }
            }
        }

        let n = more.len(); // more 和 less 长度相等
        let mut best = i32::MAX;

        // 2. 枚举 more 的所有排列，与 less 顺序配对，计算总距离的最小值
        // 使用字典序排列算法（非递归）
        let mut arr = more.clone(); // 待排列的数组
        loop {
            // 计算当前排列的总距离
            let dist: i32 = (0..n)
                .map(|i| (arr[i].0 - less[i].0).abs() + (arr[i].1 - less[i].1).abs())
                .sum();
            if dist < best {
                best = dist;
            }
            if !next_permutation(&mut arr) {
                break;
            }
        }
        best
    }
}

/// 生成下一个字典序排列（原地修改），返回 false 表示已是最后一个排列
fn next_permutation<T: Ord>(arr: &mut [T]) -> bool {
    // 1. 找到第一个升序对 (i, i+1) 从右向左
    let i = match arr.windows(2).rposition(|w| w[0] < w[1]) {
        Some(idx) => idx,
        None => return false,
    };
    // 2. 找到从右起第一个大于 arr[i] 的元素
    let j = arr.iter().rposition(|x| x > &arr[i]).unwrap();
    arr.swap(i, j);
    // 3. 反转 i+1 到末尾
    arr[i + 1..].reverse();
    true
}
```
