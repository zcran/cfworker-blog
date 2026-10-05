---
title: "leetcode-矩阵37"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 判断矩阵经轮转后是否一致

给你两个大小为 n x n 的二进制矩阵 mat 和 target 。现 以 90 度顺时针轮转 矩阵 mat 中的元素 若干次 ，如果能够使 mat 与 target 一致，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn find_rotation(mat: Vec<Vec<i32>>, target: Vec<Vec<i32>>) -> bool {
        let n = mat.len();

        // 依次比较 mat 旋转 0°、90°、180°、270° 后是否与 target 相同
        // 不必真正旋转矩阵：对每个位置 (i, j)，直接从 mat 中取旋转后对应位置的值与 target 比较
        for k in 0..4 {
            let mut same = true;
            'outer: for i in 0..n {
                for j in 0..n {
                    // 旋转 k 次 90° 后，(i, j) 处对应原矩阵中的位置
                    let (mut x, mut y) = (i, j);
                    for _ in 0..k {
                        let (nx, ny) = (y, n - 1 - x);
                        x = nx;
                        y = ny;
                    }
                    if mat[x][y] != target[i][j] {
                        same = false;
                        break 'outer; // 发现不一致，立刻跳出两层循环，无需继续比较
                    }
                }
            }
            if same {
                return true;
            }
        }

        false
    }
}
```
Rust 中普通的 break 只能跳出当前最内层循环。如果想从内层循环直接跳出外层循环，就需要给外层循环加标签：
