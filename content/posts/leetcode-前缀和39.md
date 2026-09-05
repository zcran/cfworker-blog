---
title: "leetcode-前缀和39"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 移动所有球到每个盒子所需的最小操作数

有 n 个盒子。给你一个长度为 n 的二进制字符串 boxes ，其中 boxes[i] 的值为 '0' 表示第 i 个盒子是 空 的，而 boxes[i] 的值为 '1' 表示盒子里有 一个 小球。

在一步操作中，你可以将 一个 小球从某个盒子移动到一个与之相邻的盒子中。第 i 个盒子和第 j 个盒子相邻需满足 abs(i - j) == 1 。注意，操作执行后，某些盒子中可能会存在不止一个小球。

返回一个长度为 n 的数组 answer ，其中 answer[i] 是将所有小球移动到第 i 个盒子所需的 最小 操作数。

每个 answer[i] 都需要根据盒子的 初始状态 进行计算。


```
impl Solution {
    /// 计算将所有小球移动到每个盒子的最小操作数
    ///
    /// 使用前缀和 + 状态转移：
    /// 1. 先计算将所有球移动到第 0 个盒子的操作数
    /// 2. 从左到右转移：移动到下一个盒子时，所有左侧的球多走 1 步，右侧的球少走 1 步
    pub fn min_operations(boxes: String) -> Vec<i32> {
        let boxes: Vec<i32> = boxes.bytes().map(|b| (b - b'0') as i32).collect();
        let n = boxes.len();
        let mut ans = vec![0; n];

        // 计算所有球移动到第 0 个盒子的操作数
        let mut ops = 0;
        let mut left_count = 0;      // 左侧球的数量
        let mut right_count = boxes.iter().sum::<i32>(); // 右侧球的总数

        // 第一次遍历：计算所有球到盒子 0 的距离和
        for (i, &has_ball) in boxes.iter().enumerate() {
            ops += has_ball * i as i32;
        }
        ans[0] = ops;

        // 从左到右转移
        // 从 i-1 移到 i 时：
        // - 左侧所有球距离 +1
        // - 右侧所有球距离 -1
        // 变化量 = left_count - right_count
        for i in 1..n {
            left_count += boxes[i - 1];
            right_count -= boxes[i - 1];
            ops += left_count - right_count;
            ans[i] = ops;
        }

        ans
    }
}
```
