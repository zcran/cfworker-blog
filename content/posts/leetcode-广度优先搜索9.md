---
title: "leetcode-广度优先搜索9"
date: 2026-09-13T09:32:06+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 自由之路

电子游戏“辐射4”中，任务 “通向自由” 要求玩家到达名为 “Freedom Trail Ring” 的金属表盘，并使用表盘拼写特定关键词才能开门。

给定一个字符串 ring ，表示刻在外环上的编码；给定另一个字符串 key ，表示需要拼写的关键词。您需要算出能够拼写关键词中所有字符的最少步数。

最初，ring 的第一个字符与 12:00 方向对齐。您需要顺时针或逆时针旋转 ring 以使 key 的一个字符在 12:00 方向对齐，然后按下中心按钮，以此逐个拼写完 key 中的所有字符。

旋转 ring 拼出 key 字符 key[i] 的阶段中：

1. 您可以将 ring 顺时针或逆时针旋转 一个位置 ，计为1步。旋转的最终目的是将字符串 ring 的一个字符与 12:00 方向对齐，并且这个字符必须等于字符 key[i] 。
2. 如果字符 key[i] 已经对齐到12:00方向，您需要按下中心按钮进行拼写，这也将算作 1 步。按完之后，您可以开始拼写 key 的下一个字符（下一阶段）, 直至完成所有拼写。


```
impl Solution {
    pub fn find_rotate_steps(ring: String, key: String) -> i32 {
        let ring: Vec<u8> = ring.bytes().collect();
        let key: Vec<u8> = key.bytes().collect();
        let n = ring.len();

        // 记录 ring 中每个字符出现的所有下标
        let mut pos: Vec<Vec<usize>> = vec![Vec::new(); 26];
        for (i, &c) in ring.iter().enumerate() {
            pos[(c - b'a') as usize].push(i);
        }

        // dp[j] = 拼完当前 key 字符后，12:00 方向对齐到 ring[j] 时的最小步数
        // 初始为拼写 key[0] 的代价：从 ring[0] 转到 j（顺时针/逆时针取最小）+ 按一次
        let mut dp = vec![i32::MAX; n];
        for &j in &pos[(key[0] - b'a') as usize] {
            dp[j] = j.min(n - j) as i32 + 1;
        }

        // 逐字符推进，滚动更新 dp
        for i in 1..key.len() {
            let mut next = vec![i32::MAX; n];
            for &j in &pos[(key[i] - b'a') as usize] {
                for &k in &pos[(key[i - 1] - b'a') as usize] {
                    if dp[k] == i32::MAX {
                        continue;
                    }
                    // ring 上 k -> j 的最短旋转距离（环形取顺、逆时针最小值）
                    let dist = (j.abs_diff(k)).min(n - j.abs_diff(k)) as i32;
                    next[j] = next[j].min(dp[k] + dist + 1);
                }
            }
            dp = next;
        }

        *dp.iter().min().unwrap()
    }
}
```
