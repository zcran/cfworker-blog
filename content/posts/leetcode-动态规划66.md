---
title: "leetcode-动态规划66"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 填充书架

给定一个数组 books ，其中 books[i] = [thicknessi, heighti] 表示第 i 本书的厚度和高度。你也会得到一个整数 shelfWidth 。

按顺序 将这些书摆放到总宽度为 shelfWidth 的书架上。

先选几本书放在书架上（它们的厚度之和小于等于书架的宽度 shelfWidth ），然后再建一层书架。重复这个过程，直到把所有的书都放在书架上。

需要注意的是，在上述过程的每个步骤中，摆放书的顺序与给定图书数组 books 顺序相同。

例如，如果这里有 5 本书，那么可能的一种摆放情况是：第一和第二本书放在第一层书架上，第三本书放在第二层书架上，第四和第五本书放在最后一层书架上。

每一层所摆放的书的最大高度就是这一层书架的层高，书架整体的高度为各层高之和。

以这种方式布置书架，返回书架整体可能的最小高度。


```
impl Solution {
    /// 1105. 填充书架
    ///
    /// dp[i]：摆放前 i 本书（books[0..i]）的最小总高度。
    /// 对每个 i，从第 i 本书向左尝试把 books[j..i] 放在同一层（j 递减），
    /// 只要该层厚度不超过 shelfWidth，就计算这一层的高度
    /// max_height = max(books[j..i] 的高度)，并更新：
    ///   dp[i] = min(dp[i], dp[j] + max_height)
    ///
    /// 每层书的顺序必须与原数组一致，因此逆向扩展恰好枚举了所有合法分层。
    /// 时间 O(n^2)，空间 O(n)。
    pub fn min_height_shelves(books: Vec<Vec<i32>>, shelf_width: i32) -> i32 {
        let n = books.len();
        // dp[i] 表示前 i 本书的最小总高度，dp[0] = 0
        let mut dp = vec![0i32; n + 1];

        for i in 1..=n {
            let (mut width, mut max_height) = (0, 0);
            dp[i] = i32::MAX;
            // 把 books[j-1..i] 放到同一层，j 从 i 递减到 1
            for j in (1..=i).rev() {
                width += books[j - 1][0];
                if width > shelf_width {
                    break; // 再加书就超宽，停止扩展
                }
                max_height = max_height.max(books[j - 1][1]);
                dp[i] = dp[i].min(dp[j - 1] + max_height);
            }
        }

        dp[n]
    }
}
```
