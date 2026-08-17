---
title: "leetcode-模拟96"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 重新放置石块

给你一个下标从 0 开始的整数数组 nums ，表示一些石块的初始位置。再给你两个长度 相等 下标从 0 开始的整数数组 moveFrom 和 moveTo 。

在 moveFrom.length 次操作内，你将改变石块的位置。在第 i 次操作中，你将位置在 moveFrom[i] 的所有石块移到位置 moveTo[i] 。

完成这些操作后，请你按升序返回所有 有 石块的位置。

注意：

如果一个位置至少有一个石块，我们称这个位置 有 石块。
一个位置可能会有多个石块。


```
use std::collections::HashSet;

impl Solution {
    pub fn relocate_marbles(nums: Vec<i32>, move_from: Vec<i32>, move_to: Vec<i32>) -> Vec<i32> {
        // HashSet 比 HashMap<bool> 更省内存，语义也更清晰
        let mut positions: HashSet<i32> = nums.into_iter().collect();

        for (from, to) in move_from.into_iter().zip(move_to.into_iter()) {
            // remove 返回 bool：只有 from 位置确实有石块时才插入 to
            if positions.remove(&from) {
                positions.insert(to);
            }
        }

        let mut ans: Vec<i32> = positions.into_iter().collect();
        ans.sort_unstable(); // 比 sort 更快，且本题不需要稳定性
        ans
    }
}
```
