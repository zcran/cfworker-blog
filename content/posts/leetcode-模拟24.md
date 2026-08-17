---
title: "leetcode-模拟24"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 拼车

车上最初有 capacity 个空座位。车 只能 向一个方向行驶（也就是说，不允许掉头或改变方向）

给定整数 capacity 和一个数组 trips ,  trips[i] = [numPassengersi, fromi, toi] 表示第 i 次旅行有 numPassengersi 乘客，接他们和放他们的位置分别是 fromi 和 toi 。这些位置是从汽车的初始位置向东的公里数。

当且仅当你可以在所有给定的行程中接送所有乘客时，返回 true，否则请返回 false。


```
impl Solution {
    /// 拼车问题：判断车辆容量是否足够完成所有行程
    /// 核心思路：差分数组（前缀和），记录每个位置的乘客净变化量
    /// 时间复杂度: O(n + max_to)  空间复杂度: O(max_to)
    pub fn car_pooling(trips: Vec<Vec<i32>>, capacity: i32) -> bool {
        // 找到最远的目的地，避免固定 1001 长度
        let max_to = trips.iter().map(|t| t[2]).max().unwrap_or(0) as usize + 1;
        let mut diff = vec![0; max_to];

        // 差分：上车点 +num，下车点 -num（乘客在 to 下车，to 位置不再占用座位）
        for t in trips {
            let num = t[0];
            let from = t[1] as usize;
            let to = t[2] as usize;
            diff[from] += num;
            diff[to] -= num;
        }

        // 前缀和：遍历每个位置，累加得到当前乘客数
        let mut passengers = 0;
        for delta in diff {
            passengers += delta;
            if passengers > capacity {
                return false;
            }
        }

        true
    }
}
```
