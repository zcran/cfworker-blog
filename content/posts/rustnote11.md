---
title: "Rust Note 11"
date: 2023-05-22T20:28:46+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
## 旋转图像

给定一个 n × n 的二维矩阵 matrix 表示一个图像。请你将图像顺时针旋转 90 度。

你必须在 原地 旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要 使用另一个矩阵来旋转图像。


### C 解法：
```
void swap(int* a, int* b) {
    int t = *a;
    *a = *b, *b = t;
}

void rotate(int** matrix, int matrixSize, int* matrixColSize) {
    // 水平翻转
    for (int i = 0; i < matrixSize / 2; ++i) {
        for (int j = 0; j < matrixSize; ++j) {
            swap(&matrix[i][j], &matrix[matrixSize - i - 1][j]);
        }
    }
    // 主对角线翻转
    for (int i = 0; i < matrixSize; ++i) {
        for (int j = 0; j < i; ++j) {
            swap(&matrix[i][j], &matrix[j][i]);
        }
    }
}
```


### Rust 解法：
```
impl Solution {
    pub fn rotate(matrix: &mut Vec<Vec<i32>>) {
        let m = matrix.len() - 1;
        for r in 0..m/2+1 {
            for i in r..m-r {
                let tmp = matrix[m-i][r];
                matrix[m-i][r] = matrix[m-r][m-i];
                matrix[m-r][m-i] = matrix[i][m-r];
                matrix[i][m-r] = matrix[r][i];
                matrix[r][i] = tmp;
            }
        }
    }
}
```

### Rust 解法2：
```
impl Solution {
    pub fn rotate(matrix: &mut Vec<Vec<i32>>) {
        for i in 0..matrix.len() - 1 {
            for j in i + 1..matrix.len() {
                let t = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = t;
            }
        }
        matrix.iter_mut().for_each(|v| v.reverse());
    }
}
```
