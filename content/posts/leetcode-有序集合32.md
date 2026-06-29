---
title: "leetcode-有序集合32"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 水果成篮 II

给你两个长度为 n 的整数数组，fruits 和 baskets，其中 fruits[i] 表示第 i 种水果的 数量，baskets[j] 表示第 j 个篮子的 容量。

你需要对 fruits 数组从左到右按照以下规则放置水果：

· 每种水果必须放入第一个 容量大于等于 该水果数量的 最左侧可用篮子 中。
· 每个篮子只能装 一种 水果。
· 如果一种水果 无法放入 任何篮子，它将保持 未放置。

返回所有可能分配完成后，剩余未放置的水果种类的数量。


```
impl Solution {
    pub fn num_of_unplaced_fruits(fruits: Vec<i32>, mut baskets: Vec<i32>) -> i32 {
        let n = baskets.len();
        let mut unplaced = 0;

        for fruit in fruits {
            // 找到第一个容量足够且未被占用的篮子
            let mut placed = false;
            for i in 0..n {
                if baskets[i] >= fruit {
                    baskets[i] = -1; // 标记为已占用
                    placed = true;
                    break;
                }
            }
            if !placed {
                unplaced += 1;
            }
        }

        unplaced
    }
}
```
