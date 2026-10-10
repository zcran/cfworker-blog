---
title: "leetcode-排序87"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 训练计划 I

教练使用整数数组 actions 记录一系列核心肌群训练项目编号。为增强训练趣味性，需要将所有奇数编号训练项目调整至偶数编号训练项目之前。请将调整后的训练项目编号以 数组 形式返回。


```
impl Solution {
    pub fn training_plan(mut actions: Vec<i32>) -> Vec<i32> {
        // 双指针：le 从左找偶数，ri 从右找奇数，交换后继续
        let mut le = 0;
        let mut ri = actions.len().saturating_sub(1);

        while le < ri {
            // 跳过左侧已经是奇数的元素
            while le < ri && actions[le] & 1 == 1 {
                le += 1;
            }
            // 跳过右侧已经是偶数的元素
            while le < ri && actions[ri] & 1 == 0 {
                ri -= 1;
            }
            if le < ri {
                actions.swap(le, ri);
                le += 1;
                ri -= 1;
            }
        }

        actions
    }
}
```
