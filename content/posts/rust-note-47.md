---
title: "Rust Note 47"
date: 2023-06-12T10:35:06+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 杨辉三角

给定一个非负整数 numRows，生成「杨辉三角」的前 numRows 行。

在「杨辉三角」中，每个数是它左上方和右上方的数的和。

### C 解法：
```
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ret(numRows);
        for (int i = 0; i < numRows; ++i) {
            ret[i].resize(i + 1);
            ret[i][0] = ret[i][i] = 1;
            for (int j = 1; j < i; ++j) {
                ret[i][j] = ret[i - 1][j] + ret[i - 1][j - 1];
            }
        }
        return ret;
    }
};
```


### Rust 解法：
```
impl Solution {
    pub fn makevec(number: i32) -> Vec<i32> {
       let mut r = vec![1];
       for p in 1..(number + 1) {
           if let Some(&last) = r.last() {
               r.push((last * (number + 1 - p)) / p);
           }
       }
       r
   }
    pub fn generate(num_rows: i32) -> Vec<Vec<i32>> {
       (0..num_rows).map(|row| Solution::makevec(row)).collect()
    }
}
```