---
title: "leetcode-图10"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 你能从盒子里获得的最大糖果数

给你 n 个盒子，每个盒子的格式为 [status, candies, keys, containedBoxes] ，其中：

· 状态字 status[i]：整数，如果 box[i] 是开的，那么是 1 ，否则是 0 。
· 糖果数 candies[i]: 整数，表示 box[i] 中糖果的数目。
· 钥匙 keys[i]：数组，表示你打开 box[i] 后，可以得到一些盒子的钥匙，每个元素分别为该钥匙对应盒子的下标。
· 内含的盒子 containedBoxes[i]：整数，表示放在 box[i] 里的盒子所对应的下标。

给你一个整数数组 initialBoxes，包含你最初拥有的盒子。你可以拿走每个 已打开盒子 里的所有糖果，并且可以使用其中的钥匙去开启新的盒子，并且可以使用在其中发现的其他盒子。

请你按照上述规则，返回可以获得糖果的 最大数目 。


```
impl Solution {
    pub fn max_candies(
        status: Vec<i32>,
        candies: Vec<i32>,
        keys: Vec<Vec<i32>>,
        contained_boxes: Vec<Vec<i32>>,
        initial_boxes: Vec<i32>,
    ) -> i32 {
        let n = status.len();

        // 状态标记
        let mut has_box = vec![false; n];      // 是否拥有盒子
        let mut has_key = vec![false; n];      // 是否拥有钥匙（包括初始状态和后续获得）
        let mut opened = vec![false; n];       // 是否已打开过

        // 初始钥匙状态：如果盒子初始是打开的，相当于有钥匙
        for i in 0..n {
            if status[i] == 1 {
                has_key[i] = true;
            }
        }

        use std::collections::VecDeque;
        let mut queue = VecDeque::new();
        let mut ans = 0;

        // 初始拥有的盒子
        for &b in &initial_boxes {
            let b = b as usize;
            has_box[b] = true;
            // 如果有钥匙，立即打开
            if has_key[b] && !opened[b] {
                queue.push_back(b);
                opened[b] = true;
                ans += candies[b];
            }
        }

        // BFS 处理可打开的盒子
        while let Some(box_id) = queue.pop_front() {
            // 获得的新钥匙
            for &key in &keys[box_id] {
                let key = key as usize;
                if !has_key[key] {
                    has_key[key] = true;
                    // 如果已经拥有这个盒子且未打开，可以立即打开
                    if has_box[key] && !opened[key] {
                        queue.push_back(key);
                        opened[key] = true;
                        ans += candies[key];
                    }
                }
            }

            // 发现的内含盒子
            for &inner in &contained_boxes[box_id] {
                let inner = inner as usize;
                if !has_box[inner] {
                    has_box[inner] = true;
                    // 如果有钥匙且未打开，可以立即打开
                    if has_key[inner] && !opened[inner] {
                        queue.push_back(inner);
                        opened[inner] = true;
                        ans += candies[inner];
                    }
                }
            }
        }

        ans
    }
}
```
