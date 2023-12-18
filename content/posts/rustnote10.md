---
title: "Rust Note 10"
date: 2023-05-19T21:35:55+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
## 有效的数独

请你判断一个 9 x 9 的数独是否有效。只需要 根据以下规则 ，验证已经填入的数字是否有效即可。

数字 1-9 在每一行只能出现一次。
数字 1-9 在每一列只能出现一次。
数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。
 

注意：

一个有效的数独（部分已被填充）不一定是可解的。
只需要根据以上规则，验证已经填入的数字是否有效即可。
空白格用 '.' 表示。


### C 解法：
```
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        int row[9][9] = {0};
        int col[9][9] = {0};
        int box[3][3][9] = {0};

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                char c = board[i][j];
                if (c == '.') continue;
                int n = c - '1';
                row[i][n]++;
                col[j][n]++;
                box[i / 3][j / 3][n]++;
                if (row[i][n] > 1 || col[j][n] > 1 || box[i / 3][j / 3][n] > 1) {
                    return false;
                }
            }
        }
        return true;

    }
};
```


### Rust 解法：
```
impl Solution {
    pub fn is_valid_sudoku(board: Vec<Vec<char>>) -> bool {
        let mut row = [[0; 9];9];
        let mut column = [[0; 9];9];
        let mut block = [[0; 9];9];
        for i in 0..board.len() {
            let temp = board.get(i).unwrap();
            let length = temp.len();
            for j in 0..length {
                let z = temp[j];
                
                if z != '.' {
                    let num = z.to_string().parse::<usize>().unwrap()-1;
                    if row[i][num] >= 1 {
                        return false;
                    }else {
                        row[i][num] += 1;
                    }

                    if column[j][num] >= 1 {
                        return false;
                    }else {
                        column[j][num] += 1;
                    }
                    let index = i/3 + i/3*2 + j/3;
                    if block[index][num] >= 1 {
                        return false;
                    }else {
                        block[index][num] += 1;
                    }
                }
            }
        } 
        true
    }
}
```

### Rust 解法2:
```
use std::collections::HashSet;
impl Solution {
    pub fn is_valid_sudoku(board: Vec<Vec<char>>) -> bool {
        (0..9).all(|i| {
            let mut nine = HashSet::new();
            (0..9).all(|j| {
                nine.insert(board[i][j]) || board[i][j] == '.'
            })
        })
            && (0..9).all(|i| {
                let mut nine = HashSet::new();
                (0..9).all(|j| {
                    nine.insert(board[j][i]) || board[j][i] == '.'
                })
            })
            && (0..9).all(| i| {
                let mut nine = HashSet::new();
                (0..9).all(|j| {
                    nine.insert(board[(i*3) % 9 + j/3][((i*3) / 9)*3+j%3])
                        || board[(i*3) % 9 + j/3][((i*3) / 9)*3+j%3] == '.'
                })
            })
    }
}
```