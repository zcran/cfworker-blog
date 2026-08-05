---
title: "leetcode-滑动窗口18"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 水果成篮

你正在探访一家农场，农场从左到右种植了一排果树。这些树用一个整数数组 fruits 表示，其中 fruits[i] 是第 i 棵树上的水果 种类 。

你想要尽可能多地收集水果。然而，农场的主人设定了一些严格的规矩，你必须按照要求采摘水果：

你只有 两个 篮子，并且每个篮子只能装 单一类型 的水果。每个篮子能够装的水果总量没有限制。

你可以选择任意一棵树开始采摘，你必须从 每棵 树（包括开始采摘的树）上 恰好摘一个水果 。采摘的水果应当符合篮子中的水果类型。每采摘一次，你将会向右移动到下一棵树，并继续采摘。

一旦你走到某棵树前，但水果不符合篮子的水果类型，那么就必须停止采摘。

给你一个整数数组 fruits ，返回你可以收集的水果的 最大 数目。




```
impl Solution {
    pub fn total_fruit(fruits: Vec<i32>) -> i32 {
        let mut left = 0;
        let mut max_len = 0;

        // 两个篮子：分别存储水果类型和数量，-1 表示空
        let mut type1 = -1;
        let mut count1 = 0;
        let mut type2 = -1;
        let mut count2 = 0;

        for (right, &fruit) in fruits.iter().enumerate() {
            // 当前水果属于已有类型时，增加计数
            if fruit == type1 {
                count1 += 1;
            } else if fruit == type2 {
                count2 += 1;
            } else if type1 == -1 {
                // 第一个篮子空，放入
                type1 = fruit;
                count1 = 1;
            } else if type2 == -1 {
                // 第二个篮子空，放入
                type2 = fruit;
                count2 = 1;
            } else {
                // 遇到第三种水果，收缩左边界直到只剩一种类型
                while left < right && type1 != -1 && type2 != -1 {
                    let left_fruit = fruits[left];
                    if left_fruit == type1 {
                        count1 -= 1;
                        if count1 == 0 {
                            type1 = -1;
                        }
                    } else {
                        // 必然等于 type2
                        count2 -= 1;
                        if count2 == 0 {
                            type2 = -1;
                        }
                    }
                    left += 1;
                }

                // 将当前水果放入空出来的篮子
                if type1 == -1 {
                    type1 = fruit;
                    count1 = 1;
                } else {
                    type2 = fruit;
                    count2 = 1;
                }
            }

            // 更新最大窗口长度
            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
