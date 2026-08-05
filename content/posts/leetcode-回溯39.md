---
title: "leetcode-回溯39"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 花括号展开 II

如果你熟悉 Shell 编程，那么一定了解过花括号展开，它可以用来生成任意字符串。

花括号展开的表达式可以看作一个由 花括号、逗号 和 小写英文字母 组成的字符串，定义下面几条语法规则：

· 如果只给出单一的元素 x，那么表达式表示的字符串就只有 "x"。R(x) = {x}
    · 例如，表达式 "a" 表示字符串 "a"。
      · 而表达式 "w" 就表示字符串 "w"。

· 当两个或多个表达式并列，以逗号分隔，我们取这些表达式中元素的并集。R({e_1,e_2,...}) = R(e_1) ∪ R(e_2) ∪ ...
    · 例如，表达式 "{a,b,c}" 表示字符串 "a","b","c"。
    · 而表达式 "{{a,b},{b,c}}" 也可以表示字符串 "a","b","c"。

· 要是两个或多个表达式相接，中间没有隔开时，我们从这些表达式中各取一个元素依次连接形成字符串。R(e_1 + e_2) = {a + b for (a, b) in R(e_1) × R(e_2)}
    · 例如，表达式 "{a,b}{c,d}" 表示字符串 "ac","ad","bc","bd"。

· 表达式之间允许嵌套，单一元素与表达式的连接也是允许的。
    · 例如，表达式 "a{b,c,d}" 表示字符串 "ab","ac","ad"​​​​​​。
    · 例如，表达式 "a{b,c}{d,e}f{g,h}" 可以表示字符串 "abdfg", "abdfh", "abefg", "abefh", "acdfg", "acdfh", "acefg", "acefh"。

给出表示基于给定语法规则的表达式 expression，返回它所表示的所有字符串组成的有序列表。


```
impl Solution {
    pub fn brace_expansion_ii(expression: String) -> Vec<String> {
        use std::collections::VecDeque;

        let mut result = std::collections::HashSet::new();
        let mut queue = VecDeque::new();
        queue.push_back(expression);

        // BFS展开所有花括号表达式
        while let Some(curr) = queue.pop_front() {
            let bytes = curr.as_bytes();

            // 找到第一个需要展开的闭括号位置
            let close_pos = match bytes.iter().position(|&b| b == b'}') {
                Some(pos) => pos,
                None => {
                    // 没有花括号，直接加入结果
                    result.insert(curr);
                    continue;
                }
            };

            // 找到对应的开括号位置（从闭括号往前找）
            let open_pos = (0..close_pos).rev().find(|&i| bytes[i] == b'{').unwrap();

            // 提取前缀和后缀（一次性完成，避免多次分配）
            let prefix = &curr[..open_pos];
            let suffix = &curr[close_pos + 1..];

            // 分割括号内的选项并生成新表达式
            for option in curr[open_pos + 1..close_pos].split(',') {
                let mut new_expr = String::with_capacity(prefix.len() + option.len() + suffix.len());
                new_expr.push_str(prefix);
                new_expr.push_str(option);
                new_expr.push_str(suffix);
                queue.push_back(new_expr);
            }
        }

        // 转换为有序列表
        let mut sorted: Vec<String> = result.into_iter().collect();
        sorted.sort();
        sorted
    }
}
```
