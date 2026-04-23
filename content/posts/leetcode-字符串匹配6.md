---
title: "leetcode-字符串匹配6"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 驼峰式匹配

给你一个字符串数组 queries，和一个表示模式的字符串 pattern，请你返回一个布尔数组 answer 。只有在待查项 queries[i] 与模式串 pattern 匹配时， answer[i] 才为 true，否则为 false。

如果可以将 小写字母 插入模式串 pattern 得到待查询项 queries[i]，那么待查询项与给定模式串匹配。您可以在模式串中的任何位置插入字符，也可以选择不插入任何字符。


```
impl Solution {
     pub fn camel_match(queries: Vec<String>, pattern: String) -> Vec<bool> {
        // 初始化答案数组，默认全部为 true，后续遇到不匹配时再标记为 false。
        let mut ans = vec![true; queries.len()];

        // 遍历每个查询字符串，同时获取其索引 i。
        for (i, s) in queries.into_iter().enumerate() {
            // 将 pattern 转换为字符迭代器，用于依次消费期望出现的字符。
            let mut pattern = pattern.chars();

            // `expect` 表示当前期望在查询串中匹配的 pattern 字符。
            // 初始为 pattern 的第一个字符。
            let mut expect = pattern.next();

            // 逐个遍历查询串 `s` 中的字符。
            for c in s.chars() {
                // 情况 1：当前字符 c 正好是期望的 pattern 字符。
                if Some(c) == expect {
                    // 匹配成功，将 `expect` 更新为 pattern 的下一个字符。
                    expect = pattern.next();
                }
                // 情况 2：当前字符 c 不是期望的 pattern 字符，且它是大写字母。
                else if c.is_uppercase() {
                    // 大写字母不允许被插入，必须匹配 pattern 中的某个字符。
                    // 既然它不匹配当前期望的字符，说明查询串中存在多余的大写字母，
                    // 直接判定该查询串不匹配，并跳出内部循环。
                    ans[i] = false;
                    break;
                }
                // 情况 3：c 是小写字母，且不匹配期望字符。
                // 根据规则，可以视作在 pattern 中插入了小写字母，允许继续检查下一个字符。
            }

            // 循环结束后，检查是否已经消费完 pattern 的所有字符。
            // 若 `expect` 仍有值，说明 pattern 中还有部分字符未在查询串中出现，
            // 则查询串缺失必要的大写字母，匹配失败。
            if expect.is_some() {
                ans[i] = false;
            }
        }

        ans
    }
}
```
