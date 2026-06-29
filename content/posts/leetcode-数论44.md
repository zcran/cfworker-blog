---
title: "leetcode-数论44"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 出现频率最高的质数

给你一个大小为 m x n 、下标从 0 开始的二维矩阵 mat 。在每个单元格，你可以按以下方式生成数字：

· 最多有 8 条路径可以选择：东，东南，南，西南，西，西北，北，东北。

· 选择其中一条路径，沿着这个方向移动，并且将路径上的数字添加到正在形成的数字后面。

· 注意，每一步都会生成数字，例如，如果路径上的数字是 1, 9, 1，那么在这个方向上会生成三个数字：1, 19, 191 。
返回在遍历矩阵所创建的所有数字中，出现频率最高的、大于 10的质数；如果不存在这样的质数，则返回 -1 。如果存在多个出现频率最高的质数，那么返回其中最大的那个。

注意：移动过程中不允许改变方向。


```
use std::collections::HashMap;

impl Solution {
    pub fn most_frequent_prime(mat: Vec<Vec<i32>>) -> i32 {
        let m = mat.len() as i32;
        let n = mat[0].len() as i32;

        // 8个方向：右，右下，下，左下，左，左上，上，右上
        let directions = [(1, 0), (1, 1), (0, 1), (-1, 1), (-1, 0), (-1, -1), (0, -1), (1, -1)];

        // 统计每个质数出现的次数
        let mut freq: HashMap<i32, i32> = HashMap::new();

        // 遍历每个起始单元格
        for i in 0..m {
            for j in 0..n {
                // 尝试8个方向
                for &(di, dj) in &directions {
                    let mut num = 0;
                    let (mut x, mut y) = (i, j);

                    // 沿当前方向移动
                    while x >= 0 && x < m && y >= 0 && y < n {
                        num = num * 10 + mat[x as usize][y as usize];

                        // 只记录大于10的数
                        if num > 10 {
                            // 检查是否为质数
                            if Self::is_prime(num) {
                                *freq.entry(num).or_insert(0) += 1;
                            }
                        }

                        x += di;
                        y += dj;
                    }
                }
            }
        }

        // 找出频率最高且值最大的质数
        freq.into_iter()
            .max_by(|a, b| a.1.cmp(&b.1).then(a.0.cmp(&b.0)))
            .map_or(-1, |(num, _)| num)
    }

    /// 判断一个数是否为质数
    #[inline]
    fn is_prime(num: i32) -> bool {
        if num < 2 {
            return false;
        }
        if num == 2 || num == 3 {
            return true;
        }
        if num % 2 == 0 || num % 3 == 0 {
            return false;
        }

        // 优化：只检查到 sqrt(num)，步长为6（6k±1）
        let limit = (num as f64).sqrt() as i32;
        let mut i = 5;
        while i <= limit {
            if num % i == 0 || num % (i + 2) == 0 {
                return false;
            }
            i += 6;
        }
        true
    }
}
```
