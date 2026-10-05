---
title: "leetcode-双指针52"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 救生艇

给定数组 people 。people[i]表示第 i 个人的体重 ，船的数量不限，每艘船可以承载的最大重量为 limit。

每艘船最多可同时载两人，但条件是这些人的重量之和最多为 limit。

返回 承载所有人所需的最小船数 。


```
impl Solution {
    pub fn num_rescue_boats(mut people: Vec<i32>, limit: i32) -> i32 {
        people.sort_unstable();

        let mut boats = 0;
        let mut light = 0;
        let mut heavy = people.len() as i32 - 1; // 使用 i32 避免下溢

        while light <= heavy {
            if light < heavy && people[light as usize] + people[heavy as usize] <= limit {
                light += 1;
            }
            heavy -= 1;
            boats += 1;
        }

        boats
    }
}
```
