---
title: "leetcode-数学38"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 你完成的完整对局数

一款新的在线电子游戏在近期发布，在该电子游戏中，以 刻钟 为周期规划若干时长为 15 分钟 的游戏对局。这意味着，在 HH:00、HH:15、HH:30 和 HH:45 ，将会开始一个新的对局，其中 HH 用一个从 00 到 23 的整数表示。游戏中使用 24 小时制的时钟 ，所以一天中最早的时间是 00:00 ，最晚的时间是 23:59 。

给你两个字符串 startTime 和 finishTime ，均符合 "HH:MM" 格式，分别表示你 进入 和 退出 游戏的确切时间，请计算在整个游戏会话期间，你完成的 完整对局的对局数 。

例如，如果 startTime = "05:20" 且 finishTime = "05:59" ，这意味着你仅仅完成从 05:30 到 05:45 这一个完整对局。而你没有完成从 05:15 到 05:30 的完整对局，因为你是在对局开始后进入的游戏；同时，你也没有完成从 05:45 到 06:00 的完整对局，因为你是在对局结束前退出的游戏。

如果 finishTime 早于 startTime ，这表示你玩了个通宵（也就是从 startTime 到午夜，再从午夜到 finishTime）。

假设你是从 startTime 进入游戏，并在 finishTime 退出游戏，请计算并返回你完成的 完整对局的对局数 。


```
impl Solution {
    pub fn number_of_rounds(login_time: String, logout_time: String) -> i32 {
        // 将 "HH:MM" 解析为一天内的分钟数，例如 "05:20" -> 320
        let to_minutes = |t: &str| -> i32 {
            t[0..2].parse::<i32>().unwrap() * 60 + t[3..5].parse::<i32>().unwrap()
        };

        let start = to_minutes(&login_time);
        let mut end = to_minutes(&logout_time);

        // 通宵：退出时间早于进入时间，加一天
        if end < start {
            end += 24 * 60;
        }

        // 对齐到 15 分钟的整数倍：end 之前的最后一个整点对局结束时刻
        end = end / 15 * 15;

        // 完整对局数 = 有效时长 / 15，负数说明没有完整对局
        ((end - start).max(0)) / 15
    }
}
```
