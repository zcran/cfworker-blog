---
title: "leetcode-模拟55"
date: 2026-08-08T11:31:11+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 解码斜向换位密码

字符串 originalText 使用 斜向换位密码 ，经由 行数固定 为 rows 的矩阵辅助，加密得到一个字符串 encodedText 。

originalText 先按从左上到右下的方式放置到矩阵中。


先填充蓝色单元格，接着是红色单元格，然后是黄色单元格，以此类推，直到到达 originalText 末尾。箭头指示顺序即为单元格填充顺序。所有空单元格用 ' ' 进行填充。矩阵的列数需满足：用 originalText 填充之后，最右侧列 不为空 。

接着按行将字符附加到矩阵中，构造 encodedText 。


先把蓝色单元格中的字符附加到 encodedText 中，接着是红色单元格，最后是黄色单元格。箭头指示单元格访问顺序。

例如，如果 originalText = "cipher" 且 rows = 3 ，那么我们可以按下述方法将其编码：


蓝色箭头标识 originalText 是如何放入矩阵中的，红色箭头标识形成 encodedText 的顺序。在上述例子中，encodedText = "ch   ie   pr" 。

给你编码后的字符串 encodedText 和矩阵的行数 rows ，返回源字符串 originalText 。

注意：originalText 不 含任何尾随空格 ' ' 。生成的测试用例满足 仅存在一个 可能的 originalText 。


```
impl Solution {
    pub fn decode_ciphertext(encoded_text: String, rows: i32) -> String {
        let rows = rows as usize;
        let len = encoded_text.len();
        if rows <= 1 || len == 0 {
            return encoded_text;
        }

        let cols = len / rows;
        let bytes = encoded_text.as_bytes();
        let mut res = Vec::with_capacity(len);

        // 按对角线偏移量 d 依次读取：第 d 条对角线上的单元格满足 col - row = d
        // 即对于每个 d，遍历所有行 r，取列 c = r + d（若未越界）
        for d in 0..cols {
            for r in 0..rows {
                let c = r + d;
                if c < cols {
                    res.push(bytes[r * cols + c]);
                }
            }
        }

        // 去掉 originalText 不含的尾随空格
        String::from_utf8(res).unwrap().trim_end().to_string()
    }
}
```
