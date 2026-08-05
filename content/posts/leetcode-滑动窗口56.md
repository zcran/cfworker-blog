---
title: "leetcode-滑动窗口56"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 毯子覆盖的最多白色砖块数

给你一个二维整数数组 tiles ，其中 tiles[i] = [li, ri] ，表示所有在 li <= j <= ri 之间的每个瓷砖位置 j 都被涂成了白色。

同时给你一个整数 carpetLen ，表示可以放在 任何位置 的一块毯子的长度。

请你返回使用这块毯子，最多 可以盖住多少块白色瓷砖。




```
impl Solution {
    pub fn maximum_white_tiles(mut tiles: Vec<Vec<i32>>, carpet_len: i32) -> i32 {
        // 按起始位置排序
        tiles.sort_unstable_by_key(|t| t[0]);
        let n = tiles.len();

        // 计算前缀和：pref[i] 表示前 i 个区间覆盖的总长度
        let mut pref = vec![0; n + 1];
        for i in 0..n {
            pref[i + 1] = pref[i] + tiles[i][1] - tiles[i][0] + 1;
        }

        let mut max_covered = 0;
        let mut right = 0;  // 右指针

        // 枚举每个区间作为毯子左端点所在的区间
        for left in 0..n {
            // 计算毯子右边界
            let carpet_end = tiles[left][0] + carpet_len - 1;

            // 找到最后一个完全在毯子内的区间
            while right < n && tiles[right][1] <= carpet_end {
                right += 1;
            }

            // 完全覆盖的区间总长度
            let covered = pref[right] - pref[left];

            // 处理部分覆盖的区间（如果存在）
            let partial = if right < n {
                (carpet_end - tiles[right][0] + 1).max(0)
            } else {
                0
            };

            max_covered = max_covered.max(covered + partial);
        }

        max_covered
    }
}
```
