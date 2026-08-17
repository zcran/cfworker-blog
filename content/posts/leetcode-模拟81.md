---
title: "leetcode-模拟81"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 删除每行中的最大值

给你一个 m x n 大小的矩阵 grid ，由若干正整数组成。

执行下述操作，直到 grid 变为空矩阵：

从每一行删除值最大的元素。如果存在多个这样的值，删除其中任何一个。

将删除元素中的最大值与答案相加。

注意 每执行一次操作，矩阵中列的数据就会减 1 。

返回执行上述操作后的答案。


```
impl Solution {
    /// 每轮从每行删最大值，等价于每行排序后按列取最大值再求和。
    pub fn delete_greatest_value(mut grid: Vec<Vec<i32>>) -> i32 {
        for row in &mut grid {
            row.sort_unstable();
        }
        let (first, rest) = grid.split_at_mut(1);
        let max_row = &mut first[0];
        for row in rest {
            for (j, &val) in row.iter().enumerate() {
                max_row[j] = max_row[j].max(val);
            }
        }
        max_row.iter().sum()
    }
}
```
