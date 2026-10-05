---
title: "leetcode-双指针88"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 移动片段得到字符串

给你两个字符串 start 和 target ，长度均为 n 。每个字符串 仅 由字符 'L'、'R' 和 '_' 组成，其中：

字符 'L' 和 'R' 表示片段，其中片段 'L' 只有在其左侧直接存在一个 空位 时才能向 左 移动，而片段 'R' 只有在其右侧直接存在一个 空位 时才能向 右 移动。

字符 '_' 表示可以被 任意 'L' 或 'R' 片段占据的空位。

如果在移动字符串 start 中的片段任意次之后可以得到字符串 target ，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn can_change(start: String, target: String) -> bool {
        let (s, t) = (start.as_bytes(), target.as_bytes());
        let (n, mut i, mut j) = (s.len(), 0, 0);

        while i < n || j < n {
            // 跳过空位
            while i < n && s[i] == b'_' { i += 1; }
            while j < n && t[j] == b'_' { j += 1; }

            // 同时到达末尾，匹配成功
            if i == n && j == n { return true; }
            // 仅一方到达末尾，无法匹配
            if i == n || j == n { return false; }

            // 字符不同，或移动方向不允许时，匹配失败
            if s[i] != t[j]
                || (s[i] == b'L' && i < j)  // L 只能左移，因此原下标必须 >= 目标下标
                || (s[i] == b'R' && i > j)  // R 只能右移，因此原下标必须 <= 目标下标
            {
                return false;
            }

            i += 1;
            j += 1;
        }

        true
    }
}
```
