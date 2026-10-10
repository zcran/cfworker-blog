---
title: "leetcode-排序51"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 不含特殊楼层的最大连续楼层数

Alice 管理着一家公司，并租用大楼的部分楼层作为办公空间。Alice 决定将一些楼层作为 特殊楼层 ，仅用于放松。

给你两个整数 bottom 和 top ，表示 Alice 租用了从 bottom 到 top（含 bottom 和 top 在内）的所有楼层。另给你一个整数数组 special ，其中 special[i] 表示  Alice 指定用于放松的特殊楼层。

返回不含特殊楼层的 最大 连续楼层数。


```
impl Solution {
    /// 返回不含特殊楼层的最大连续楼层数。
    ///
    /// 思路：
    /// 在 special 中加入 bottom-1 和 top+1 作为哨兵，
    /// 排序后相邻两个特殊楼层之间的非特殊楼层数为 gap - 1，
    /// 取所有间隔的最大值即可。
    pub fn max_consecutive(bottom: i32, top: i32, mut special: Vec<i32>) -> i32 {
        special.push(bottom - 1);
        special.push(top + 1);
        special.sort_unstable();

        special
            .windows(2)
            .map(|w| w[1] - w[0] - 1)
            .max()
            .unwrap()
    }
}
```
