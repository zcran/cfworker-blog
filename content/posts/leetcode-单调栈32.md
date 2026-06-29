---
title: "leetcode-单调栈32"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 游戏中弱角色的数量

你正在参加一个多角色游戏，每个角色都有两个主要属性：攻击 和 防御 。给你一个二维整数数组 properties ，其中 properties[i] = [attacki, defensei] 表示游戏中第 i 个角色的属性。

如果存在一个其他角色的攻击和防御等级 都严格高于 该角色的攻击和防御等级，则认为该角色为 弱角色 。更正式地，如果认为角色 i 弱于 存在的另一个角色 j ，那么 attackj > attacki 且 defensej > defensei 。

返回 弱角色 的数量。

```
impl Solution {
    pub fn number_of_weak_characters(properties: Vec<Vec<i32>>) -> i32 {
        let mut chars = properties;

        // 攻击力升序，防御力降序
        // 这样从后向前遍历时，攻击力大的先出现，且同攻击力中防御力大的先出现
        chars.sort_unstable_by(|a, b| {
            a[0].cmp(&b[0]).then(b[1].cmp(&a[1]))
        });

        let mut ans = 0;
        let mut max_d = 0;

        for p in chars.into_iter().rev() {
            if p[1] < max_d {
                ans += 1;
            } else {
                max_d = p[1];
            }
        }

        ans
    }
}
```
