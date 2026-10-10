---
title: "leetcode-字符串48"
date: 2026-10-10T12:56:55+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## Excel 表中某个范围内的单元格

Excel 表中的一个单元格 (r, c) 会以字符串 "<col><row>" 的形式进行表示，其中：

· <col> 即单元格的列号 c 。用英文字母表中的 字母 标识。
    · 例如，第 1 列用 'A' 表示，第 2 列用 'B' 表示，第 3 列用 'C' 表示，以此类推。
· <row> 即单元格的行号 r 。第 r 行就用 整数 r 标识。

给你一个格式为 "<col1><row1>:<col2><row2>" 的字符串 s ，其中 <col1> 表示 c1 列，<row1> 表示 r1 行，<col2> 表示 c2 列，<row2> 表示 r2 行，并满足 r1 <= r2 且 c1 <= c2 。

找出所有满足 r1 <= x <= r2 且 c1 <= y <= c2 的单元格，并以列表形式返回。单元格应该按前面描述的格式用 字符串 表示，并以 非递减 顺序排列（先按列排，再按行排）。


```
impl Solution {
    pub fn cells_in_range(s: String) -> Vec<String> {
        let b = s.as_bytes();
        let (c1, r1, c2, r2) = (b[0], b[1], b[3], b[4]);

        // 列优先、行次之，按非递减顺序生成所有单元格
        let mut ans = Vec::with_capacity(
            ((c2 - c1 + 1) as usize) * ((r2 - r1 + 1) as usize),
        );
        for c in c1..=c2 {
            for r in r1..=r2 {
                ans.push(format!("{}{}", c as char, r as char));
            }
        }
        ans
    }
}
```
