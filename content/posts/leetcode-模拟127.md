---
title: "leetcode-模拟127"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 用特殊操作处理字符串 I

给你一个字符串 s，它由小写英文字母和特殊字符：*、# 和 % 组成。

请根据以下规则从左到右处理 s 中的字符，构造一个新的字符串 result：

如果字符是 小写 英文字母，则将其添加到 result 中。
字符 '*' 会 删除 result 中的最后一个字符（如果存在）。
字符 '#' 会 复制 当前的 result 并 追加 到其自身后面。
字符 '%' 会 反转 当前的 result。

在处理完 s 中的所有字符后，返回最终的字符串 result。


```
impl Solution {
    /// 根据规则处理字符串 s，构造最终结果
    ///
    /// 规则：
    /// - 小写字母：添加到 result
    /// - '*'：删除 result 最后一个字符（如果存在）
    /// - '#'：复制当前 result 并追加到自身后面
    /// - '%'：反转当前 result
    ///
    /// # 参数
    /// - `s`: 输入字符串，由小写字母和 '*', '#', '%' 组成
    ///
    /// # 返回值
    /// - 处理后的最终字符串
    pub fn process_str(s: String) -> String {
        let mut result = String::new();

        for ch in s.chars() {
            match ch {
                '*' => {
                    // 删除最后一个字符（如果存在）
                    result.pop();
                }
                '#' => {
                    // 复制当前 result 并追加到自身
                    // 先保存当前内容，再追加，避免借用冲突
                    let copy = result.clone();
                    result.push_str(&copy);
                }
                '%' => {
                    // 反转当前 result
                    // 使用 unsafe 的 as_mut_vec 进行原地反转，避免分配新内存
                    unsafe { result.as_mut_vec().reverse() };
                }
                c => {
                    // 小写字母直接添加
                    result.push(c);
                }
            }
        }

        result
    }
}
```
