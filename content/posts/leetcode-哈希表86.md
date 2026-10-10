---
title: "leetcode-哈希表86"
date: 2026-10-09T20:08:01+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 合并靠近字符

给你一个由小写英文字母组成的字符串 s 和一个整数 k。

在 当前 字符串 s 中，如果两个 相同 字符之间的下标距离 至多 为 k，则认为它们是 靠近 的。

当两个字符 靠近 时，右侧的字符会合并到左侧。合并操作 逐个 发生，每次合并后，字符串都会更新，直到无法再进行合并为止。

返回执行所有可能合并后的最终字符串。

注意：如果可以进行多次合并，请始终选择 左侧下标最小 的那一对进行合并。如果多对字符共享最小的左侧下标，请选择 右侧下标最小 的那一对。


```
impl Solution {
    pub fn merge_characters(s: String, k: i32) -> String {
        // pos[c] = 字符 c 在结果中最后出现的位置（用 i32 便于处理"从未出现"）
        // 初值取一个足够小的负数，保证第一次遇到 c 时一定会写入
        let mut pos = [i32::MIN / 2; 26];
        let mut result: Vec<u8> = Vec::with_capacity(s.len());

        for b in s.bytes() {
            let idx = (b - b'a') as usize;
            // 若当前字符与它上次写入的位置距离超过 k，则不能合并，需保留
            if result.len() as i32 - pos[idx] > k {
                pos[idx] = result.len() as i32;
                result.push(b);
            }
            // 否则该字符与左侧同字符合并，直接丢弃
        }

        String::from_utf8(result).unwrap()
    }
}
```
