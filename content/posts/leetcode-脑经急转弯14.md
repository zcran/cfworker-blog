---
title: "leetcode-脑经急转弯14"
date: 2026-03-24T20:55:57+08:00
tags: ["leetcode", "脑筋急转弯"]
draft: false
---


## 字符串元音游戏

小红和小明在玩一个字符串元音游戏。

给你一个字符串 s，小红和小明将轮流参与游戏，小红 先 开始：

在小红的回合，她必须移除 s 中包含 奇数 个元音的任意 非空 子字符串。
在小明的回合，他必须移除 s 中包含 偶数 个元音的任意 非空 子字符串。
第一个无法在其回合内进行移除操作的玩家输掉游戏。假设小红和小明都采取 最优策略 。

如果小红赢得游戏，返回 true，否则返回 false。

英文元音字母包括：a, e, i, o, 和 u。



```
impl Solution {
    pub fn does_alice_win(s: String) -> bool {
        s.chars()
        .any(|c| matches!(c, 'a' | 'e' | 'i' | 'o' | 'u'))
        }
}
```


如果需要大小写不敏感，可以改为：

s.chars().any(|c| matches!(c.to_ascii_lowercase(), 'a' | 'e' | 'i' | 'o' | 'u'))
