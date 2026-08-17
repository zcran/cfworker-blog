---
title: "leetcode-模拟117"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 出现在屏幕上的字符串序列

给你一个字符串 target。

Alice 将会使用一种特殊的键盘在她的电脑上输入 target，这个键盘 只有两个 按键：

· 按键 1：在屏幕上的字符串后追加字符 'a'。
· 按键 2：将屏幕上字符串的 最后一个 字符更改为英文字母表中的 下一个 字符。例如，'c' 变为 'd'，'z' 变为 'a'。

注意，最初屏幕上是一个空字符串 ""，所以她 只能 按按键 1。

请你考虑按键次数 最少 的情况，按字符串出现顺序，返回 Alice 输入 target 时屏幕上出现的所有字符串列表。


```
impl Solution {
    /// 模拟 Alice 使用双键键盘输入 target 的所有中间状态
    ///
    /// 键盘规则：
    /// - 按键1：追加 'a'
    /// - 按键2：最后一个字符循环递增（'a'→'b'→...→'z'→'a'）
    ///
    /// 策略：对每个目标字符，先按按键1追加 'a'，再按按键2逐步递增到目标字符
    ///
    /// # 参数
    /// - `target`: 目标字符串
    ///
    /// # 返回值
    /// - 按出现顺序的所有屏幕字符串列表
    pub fn string_sequence(target: String) -> Vec<String> {
        let mut result = Vec::new();
        let mut current = String::new();

        for target_char in target.chars() {
            // 按键1：追加 'a' 作为占位
            current.push('a');
            result.push(current.clone());

            // 按键2：将最后一个字符从 'a' 逐步递增到 target_char
            // 循环次数 = target_char 与 'a' 的字母表距离
            for _ in 0..(target_char as u8 - b'a') {
                // 先 pop 最后一个字符，转为 u8 加 1 后再 push 回去
                let last = current.pop().unwrap();
                current.push((last as u8 + 1) as char);
                result.push(current.clone());
            }
        }

        result
    }
}
```
