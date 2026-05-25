---
title: "leetcode-队列21"
date: 2026-05-23T09:21:38+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 招式拆解 II

某套连招动作记作仅由小写字母组成的序列 arr，其中 arr[i] 第 i 个招式的名字。请返回第一个只出现一次的招式名称，如不存在请返回空格。


```
impl Solution {
    /// 返回字符串中第一个只出现一次的字符，若不存在则返回空格
    /// 题目保证字符串仅由小写字母组成，故使用固定长度数组计数
    pub fn dismantling_action(arr: String) -> char {
        let mut cnt = [0; 26];
        // 第一遍：统计每个字母出现次数
        for c in arr.chars() {
            cnt[(c as u8 - b'a') as usize] += 1;
        }
        // 第二遍：找到第一个计数为 1 的字符
        for c in arr.chars() {
            if cnt[(c as u8 - b'a') as usize] == 1 {
                return c;
            }
        }
        ' '
    }
}
```
