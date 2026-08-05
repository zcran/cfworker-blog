---
title: "leetcode-滑动窗口119"
date: 2026-07-18T11:02:36+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 招式拆解 I

某套连招动作记作序列 arr，其中 arr[i] 为第 i 个招式的名字。请返回 arr 中最多可以出连续不重复的多少个招式。


```
impl Solution {
    pub fn dismantling_action(arr: String) -> i32 {
        let bytes = arr.as_bytes();
        let mut last_pos = vec![0; 128]; // 记录每个字符上一次出现的位置
        let mut start = 0;
        let mut max_len = 0;

        for (end, &ch) in bytes.iter().enumerate() {
            let idx = ch as usize;
            // 如果当前字符在窗口内出现过，移动左边界到上一次出现位置之后
            if last_pos[idx] > start {
                start = last_pos[idx];
            }
            // 更新当前字符的最新位置
            last_pos[idx] = end + 1; // 存储位置 + 1，便于计算
            // 更新最大长度
            max_len = max_len.max(end - start + 1);
        }

        max_len as i32
    }
}
```
