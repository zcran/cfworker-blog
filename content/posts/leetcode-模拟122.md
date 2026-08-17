---
title: "leetcode-模拟122"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 统计用户被提及情况

给你一个整数 numberOfUsers 表示用户总数，另有一个大小为 n x 3 的数组 events 。

每个 events[i] 都属于下述两种类型之一：

1. 消息事件（Message Event）：["MESSAGE", "timestampi", "mentions_stringi"]
    · 事件表示在 timestampi 时，一组用户被消息提及。
    · mentions_stringi 字符串包含下述标识符之一：
        · id<number>：其中 <number> 是一个区间 [0,numberOfUsers - 1] 内的整数。可以用单个空格分隔 多个 id ，并且 id 可能重复。此外，这种形式可以提及离线用户。
        · ALL：提及 所有 用户。
        · HERE：提及所有 在线 用户。

2. 离线事件（Offline Event）：["OFFLINE", "timestampi", "idi"]
    · 事件表示用户 idi 在 timestampi 时变为离线状态 60 个单位时间。用户会在 timestampi + 60 时自动再次上线。

返回数组 mentions ，其中 mentions[i] 表示  id 为  i 的用户在所有 MESSAGE 事件中被提及的次数。

最初所有用户都处于在线状态，并且如果某个用户离线或者重新上线，其对应的状态变更将会在所有相同时间发生的消息事件之前进行处理和同步。

注意 在单条消息中，同一个用户可能会被提及多次。每次提及都需要被 分别 统计。


```
impl Solution {
    /// 统计每个用户在所有 MESSAGE 事件中被提及的次数
    ///
    /// 事件类型：
    /// - MESSAGE: ["MESSAGE", timestamp, mentions_string]
    ///   · "ALL" — 提及所有用户
    ///   · "HERE" — 提及所有在线用户（next_online <= 当前时间）
    ///   · "id0 id1 ..." — 提及指定用户（可重复，离线用户也可被提及）
    /// - OFFLINE: ["OFFLINE", timestamp, user_id]
    ///   · 用户在 timestamp 离线，timestamp + 60 自动上线
    ///
    /// 关键规则：相同时间下，OFFLINE 事件优先于 MESSAGE 事件处理
    ///
    /// # 参数
    /// - `number_of_users`: 用户总数
    /// - `events`: 事件列表，每个事件为 [类型, 时间戳, 参数] 的字符串数组
    ///
    /// # 返回值
    /// - 每个用户被提及的次数数组
    pub fn count_mentions(number_of_users: i32, mut events: Vec<Vec<String>>) -> Vec<i32> {
        let n = number_of_users as usize;

        // 按时间戳升序排序；时间相同时，OFFLINE 事件排在 MESSAGE 前面
        // 利用布尔值排序：false (OFFLINE) < true (MESSAGE)
        events.sort_by(|a, b| {
            let time_a = a[1].parse::<i32>().unwrap();
            let time_b = b[1].parse::<i32>().unwrap();
            time_a.cmp(&time_b).then_with(|| {
                let is_msg_a = a[0] == "MESSAGE";
                let is_msg_b = b[0] == "MESSAGE";
                is_msg_a.cmp(&is_msg_b) // false < true，即 OFFLINE 优先
            })
        });

        let mut mentions = vec![0i32; n];
        // 记录每个用户下次上线的时间，0 表示当前在线
        let mut next_online = vec![0i32; n];

        for event in events {
            let timestamp = event[1].parse::<i32>().unwrap();

            match event[0].as_str() {
                "MESSAGE" => {
                    let target = event[2].as_str();
                    match target {
                        "ALL" => {
                            // 提及所有用户，无论在线状态
                            mentions.iter_mut().for_each(|c| *c += 1);
                        }
                        "HERE" => {
                            // 提及所有当前在线的用户
                            for i in 0..n {
                                if next_online[i] <= timestamp {
                                    mentions[i] += 1;
                                }
                            }
                        }
                        ids => {
                            // 解析 "id0 id1 ..." 格式的提及列表
                            for id_str in ids.split_whitespace() {
                                // 跳过 "id" 前缀，解析数字
                                let user_id: usize = id_str[2..].parse().unwrap();
                                mentions[user_id] += 1;
                            }
                        }
                    }
                }
                _ => {
                    // OFFLINE 事件：设置用户下次上线时间为当前时间 + 60
                    let user_id: usize = event[2].parse().unwrap();
                    next_online[user_id] = timestamp + 60;
                }
            }
        }

        mentions
    }
}
```
