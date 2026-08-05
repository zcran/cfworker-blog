---
title: "leetcode-栈18"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 原子的数量

给你一个字符串化学式 formula ，返回 每种原子的数量 。

原子总是以一个大写字母开始，接着跟随 0 个或任意个小写字母，表示原子的名字。

如果数量大于 1，原子后会跟着数字表示原子的数量。如果数量等于 1 则不会跟数字。

    · 例如，"H2O" 和 "H2O2" 是可行的，但 "H1O2" 这个表达是不可行的。

两个化学式连在一起可以构成新的化学式。

    · 例如 "H2O2He3Mg4" 也是化学式。

由括号括起的化学式并佐以数字（可选择性添加）也是化学式。

    · 例如 "(H2O2)" 和 "(H2O2)3" 是化学式。

返回所有原子的数量，格式为：第一个（按字典序）原子的名字，跟着它的数量（如果数量大于 1），然后是第二个原子的名字（按字典序），跟着它的数量（如果数量大于 1），以此类推。


```
// 化学式原子计数
// 使用栈 + BTreeMap，支持括号和数字倍数
use std::collections::BTreeMap;

impl Solution {
    pub fn count_of_atoms(formula: String) -> String {
        let bytes = formula.as_bytes();
        let mut stack = vec![BTreeMap::new()]; // 每个元素是一层的作用域
        let mut i = 0;
        let n = bytes.len();

        while i < n {
            match bytes[i] {
                // 大写字母：开始解析原子名
                b'A'..=b'Z' => {
                    let start = i;
                    i += 1;
                    while i < n && bytes[i].is_ascii_lowercase() {
                        i += 1;
                    }
                    let name = &bytes[start..i];

                    // 解析数字
                    let mut count = 1;
                    if i < n && bytes[i].is_ascii_digit() {
                        count = 0;
                        while i < n && bytes[i].is_ascii_digit() {
                            count = count * 10 + (bytes[i] - b'0') as i32;
                            i += 1;
                        }
                    }

                    // 添加到当前层
                    *stack.last_mut().unwrap().entry(name).or_insert(0) += count;
                }

                // 左括号：新作用域
                b'(' => {
                    stack.push(BTreeMap::new());
                    i += 1;
                }

                // 右括号：结束作用域，应用倍数
                b')' => {
                    i += 1;
                    let mut multiplier = 1;
                    if i < n && bytes[i].is_ascii_digit() {
                        multiplier = 0;
                        while i < n && bytes[i].is_ascii_digit() {
                            multiplier = multiplier * 10 + (bytes[i] - b'0') as i32;
                            i += 1;
                        }
                    }

                    // 弹出当前层，乘以倍数，合并到上一层
                    let mut map = stack.pop().unwrap();
                    if multiplier > 1 {
                        for count in map.values_mut() {
                            *count *= multiplier;
                        }
                    }
                    let parent = stack.last_mut().unwrap();
                    for (name, count) in map {
                        *parent.entry(name).or_insert(0) += count;
                    }
                }

                _ => unreachable!(),
            }
        }

        // 构建结果字符串（按字典序）
        let mut result = String::new();
        for (name, &count) in stack[0].iter() {
            result.push_str(std::str::from_utf8(name).unwrap());
            if count > 1 {
                result.push_str(&count.to_string());
            }
        }
        result
    }
}
```
