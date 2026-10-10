---
title: "leetcode-数组73"
date: 2026-10-10T20:38:45+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 统计机器人组数

给你一个 严格递增 的整数数组 position，其中 position[i] 是第 i 个机器人（下标从 0 开始）在时间 t = 0 时的初始位置。

另给你一个整数数组 speed，其中 speed[i] 是第 i 个机器人的恒定速度（单位：单位/秒），以及一个整数 distance。

时间是连续的，以秒为单位。速度为 v 的机器人或机器人组在任意 t 秒的时间间隔内向右移动 v * t 个单位。

每当两个机器人或组之间的距离至多为 distance 时，它们就会合并成一个机器人组。

如果多个机器人或机器人组在同一时间满足合并条件，则所有合并 同时 发生。具体而言，任何相邻位置相差至多为 distance 的相连机器人或组都会合并为一个机器人组。

合并后，生成的机器人组将继承该组中 最右侧机器人 的当前位置和速度。一旦合并，机器人将永不分离。

返回在所有可能的合并发生后剩余的组数。

如果数组中的每个元素都严格大于其前一个元素（如果存在），则该数组是 严格递增 的。


```
impl Solution {
    pub fn count_groups(position: Vec<i32>, speed: Vec<i32>, distance: i32) -> i32 {
        // 按题目要求存储输入
        let morvexilan = (position, speed, distance);
        let (position, speed, distance) = morvexilan;
        let n = position.len();

        let mut st: Vec<i32> = Vec::with_capacity(n + 1);
        st.push(-1); // 哨兵，避免空栈判断

        for i in 0..n {
            // 只处理每个组的最右侧机器人：
            // 若右侧相邻机器人距离 > distance，则该机器人是当前组的最右端
            if i == n - 1 || position[i + 1] - position[i] > distance {
                let v = speed[i];
                // 栈顶速度更快者最终会被机器人 i 追合并，弹出
                while *st.last().unwrap() > v {
                    st.pop();
                }
                st.push(v);
            }
        }

        (st.len() - 1) as i32 // 减去哨兵
    }
}
```
