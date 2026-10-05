---
title: "leetcode-贪心49"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 做菜顺序

一个厨师收集了他 n 道菜的满意程度 satisfaction ，这个厨师做出每道菜的时间都是 1 单位时间。

一道菜的 「 like-time 系数 」定义为烹饪这道菜结束的时间（包含之前每道菜所花费的时间）乘以这道菜的满意程度，也就是 time[i]*satisfaction[i] 。

返回厨师在准备了一定数量的菜肴后可以获得的最大 like-time 系数 总和。

你可以按 任意 顺序安排做菜的顺序，你也可以选择放弃做某些菜来获得更大的总和。




```
impl Solution {
    pub fn max_satisfaction(mut satisfaction: Vec<i32>) -> i32 {
        // 降序排列：满意度高的菜越晚做，系数越大
        satisfaction.sort_unstable_by(|a, b| b.cmp(a));

        let mut total = 0; // 当前 like-time 系数总和
        let mut prefix = 0; // 已选菜品的满意度前缀和
        for &x in &satisfaction {
            prefix += x;
            // 前缀和一旦非正，后续累加只会更小，立即停止
            if prefix <= 0 {
                break;
            }
            total += prefix;
        }
        total
    }
}
```
