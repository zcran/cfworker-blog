---
title: "leetcode-滑动窗口25"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 移动石子直到连续 II

在 X 轴上有一些不同位置的石子。给定一个整数数组 stones 表示石子的位置。

如果一个石子在最小或最大的位置，称其为 端点石子。每个回合，你可以将一颗 端点石子 拿起并移动到一个未占用的位置，使得该石子不再是一颗 端点石子。

值得注意的是，如果石子像 stones = [1,2,5] 这样，你将 无法 移动位于位置 5 的端点石子，因为无论将它移动到任何位置（例如 0 或 3），该石子都仍然会是端点石子。

当你无法进行任何移动时，即，这些石子的位置连续时，游戏结束。

以长度为 2 的数组形式返回答案，其中：

answer[0] 是你可以移动的最小次数
answer[1] 是你可以移动的最大次数。


```
impl Solution {
    pub fn num_moves_stones_ii(mut stones: Vec<i32>) -> Vec<i32> {
        stones.sort_unstable();
        let n = stones.len();

        // 最大移动次数：将石子向中间移动
        // 可以从两端各留一个端点，把中间所有空位都填满
        // 公式：max(stones[n-1] - stones[1] - (n-2), stones[n-2] - stones[0] - (n-2))
        let max_moves = (stones[n - 1] - stones[1] - (n as i32 - 2))
            .max(stones[n - 2] - stones[0] - (n as i32 - 2));

        // 最小移动次数：找到最长的"连续区间"，使区间内石子数量最多
        let mut min_moves = n;
        let mut left = 0;

        for right in 0..n {
            // 保持窗口内最大值-最小值 < n（正好覆盖n个位置）
            while stones[right] - stones[left] >= n as i32 {
                left += 1;
            }
            let window_len = right - left + 1;

            // 特殊情况：窗口内已有 n-1 个石子，且空隙为 n-2
            // 此时需要移动 2 次（例：[1,2,3,5] 或 [1,3,4,5]）
            if window_len == n - 1 && stones[right] - stones[left] == n as i32 - 2 {
                min_moves = min_moves.min(2);
            } else {
                min_moves = min_moves.min(n - window_len);
            }
        }

        vec![min_moves as i32, max_moves]
    }
}
```
