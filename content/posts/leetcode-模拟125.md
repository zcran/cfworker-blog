---
title: "leetcode-模拟125"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 执行指令后的得分

给你两个数组：instructions 和 values，数组的长度均为 n。

你需要根据以下规则模拟一个过程：

· 从下标 i = 0 的第一个指令开始，初始得分为 0。
· 如果 instructions[i] 是 "add"：
    · 将 values[i] 加到你的得分中。
    · 移动到下一个指令 (i + 1)。
· 如果 instructions[i] 是 "jump"：
    · 移动到下标为 (i + values[i]) 的指令，但不修改你的得分。

当以下任一情况发生时，过程会终止：

· 越界（即 i < 0 或 i >= n），或
· 尝试再次执行已经执行过的指令。被重复访问的指令不会再次执行。

返回过程结束时的得分。


```
impl Solution {
    /// 模拟指令执行过程，返回最终得分
    ///
    /// 指令规则：
    /// - "add"：将 values[i] 加到得分，移动到 i + 1
    /// - "jump"：移动到下标 i + values[i]，得分不变
    ///
    /// 终止条件：
    /// - 下标越界（i < 0 或 i >= n）
    /// - 尝试访问已执行过的指令（防止无限循环）
    ///
    /// # 参数
    /// - `instructions`: 指令数组，元素为 "add" 或 "jump"
    /// - `values`: 与指令对应的数值数组
    ///
    /// # 返回值
    /// - 过程结束时的得分
    pub fn calculate_score(instructions: Vec<String>, values: Vec<i32>) -> i64 {
        let n = instructions.len();
        let mut visited = vec![false; n];
        let mut score: i64 = 0;
        let mut idx: i32 = 0;

        while idx >= 0 && idx < n as i32 && !visited[idx as usize] {
            let i = idx as usize;
            visited[i] = true;

            // 根据指令首字符判断类型：'a' 表示 "add"，'j' 表示 "jump"
            match instructions[i].as_bytes()[0] {
                b'a' => {
                    score += values[i] as i64;
                    idx += 1;
                }
                _ => {
                    idx += values[i];
                }
            }
        }

        score
    }
}
```
