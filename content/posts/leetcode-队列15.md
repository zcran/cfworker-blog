---
title: "leetcode-队列15"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 知道秘密的人数


在第 1 天，有一个人发现了一个秘密。

给你一个整数 delay ，表示每个人会在发现秘密后的 delay 天之后，每天 给一个新的人 分享 秘密。同时给你一个整数 forget ，表示每个人在发现秘密 forget 天之后会 忘记 这个秘密。一个人 不能 在忘记秘密那一天及之后的日子里分享秘密。

给你一个整数 n ，请你返回在第 n 天结束时，知道秘密的人数。由于答案可能会很大，请你将结果对 109 + 7 取余 后返回。


```
use std::collections::VecDeque;

const MOD: i32 = 1000000007;

impl Solution {
    pub fn people_aware_of_secret(n: i32, delay: i32, forget: i32) -> i32 {
        let n = n as usize;
        let delay = delay as usize;
        let forget = forget as usize;

        let mut know: VecDeque<(usize, i32)> = VecDeque::new();
        let mut share: VecDeque<(usize, i32)> = VecDeque::new();
        know.push_back((1, 1));
        let mut know_cnt = 1;
        let mut share_cnt = 0;

        for i in 2..=n {
            if let Some(&(day, count)) = know.front() {
                if day == i - delay {
                    let (day, count) = know.pop_front().unwrap();
                    know_cnt = (know_cnt - count + MOD) % MOD;
                    share_cnt = (share_cnt + count) % MOD;
                    share.push_back((day, count));
                }
            }

            if let Some(&(day, count)) = share.front() {
                if day == i - forget {
                    let (_, count) = share.pop_front().unwrap();
                    share_cnt = (share_cnt - count + MOD) % MOD;
                }
            }

            if !share.is_empty() {
                know_cnt = (know_cnt + share_cnt) % MOD;
                know.push_back((i, share_cnt));
            }
        }

        (know_cnt + share_cnt) % MOD
    }
}
```
