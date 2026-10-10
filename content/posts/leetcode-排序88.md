---
title: "leetcode-排序88"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 文物朝代判断

展览馆展出来自 13 个朝代的文物，每排展柜展出 5 个文物。某排文物的摆放情况记录于数组 places，其中 places[i] 表示处于第 i 位文物的所属朝代编号。其中，编号为 0 的朝代表示未知朝代。请判断并返回这排文物的所属朝代编号是否能够视为连续的五个朝代（如遇未知朝代可算作连续情况）。


```
impl Solution {
    pub fn check_dynasty(mut places: Vec<i32>) -> bool {
        places.sort_unstable();

        // 统计 0（未知朝代）的个数；检查非 0 元素是否有重复
        let zeros = places.iter().take_while(|&&x| x == 0).count();
        for w in places[zeros..].windows(2) {
            if w[0] == w[1] {
                return false;
            }
        }

        // 非 0 元素的极差不能超过 4，且空缺位置可由 0 补齐
        // （非 0 元素已经互不相同，排序后必然构成一段无重复的序列）
        let min = places[zeros];
        let max = places[places.len() - 1];
        max - min <= 4
    }
}
```
