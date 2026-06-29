---
title: "leetcode-分治38"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---



## 寻找目标值 - 二维数组

m*n 的二维数组 plants 记录了园林景观的植物排布情况，具有以下特性：

每行中，每棵植物的右侧相邻植物不矮于该植物；
每列中，每棵植物的下侧相邻植物不矮于该植物。


请判断 plants 中是否存在目标高度值 target。

```
use std::cmp::Ordering;

impl Solution {
    pub fn find_target_in2_d_plants(plants: Vec<Vec<i32>>, target: i32) -> bool {
        if plants.is_empty() {
            return false;
        }
        let mut i = 0;
        let mut j = plants[0].len() as i32 - 1; // 从右上角开始
        while i < plants.len() && j >= 0 { // 还有剩余元素
            match plants[i][j as usize].cmp(&target) {
                Ordering::Equal => return true, // 找到 target
                Ordering::Less => i += 1, // 这一行剩余元素全部小于 target，排除
                _ => j -= 1, // 这一列剩余元素全部大于 target，排除
            }
        }
        false
    }
}
```
