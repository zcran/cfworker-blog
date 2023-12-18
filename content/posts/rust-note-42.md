---
title: "Rust Note 42"
date: 2023-06-12T10:35:05+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 3的幂

给定一个整数，写一个函数来判断它是否是 3 的幂次方。如果是，返回 true ；否则，返回 false 。

整数 n 是 3 的幂次方需满足：存在整数 x 使得 n == 3^x

### C 解法：
```
 // 在题目给定的32位有符号整数的范围内，最大的3的幂为 3^19 =1162261467。我们只需要判断n是否是3的约数即可。
class Solution {
public:
    bool isPowerOfThree(int n) {
        return n > 0 && 1162261467 % n == 0;
    }
};
```


### Rust 解法：
```
impl Solution {
    pub fn is_power_of_three(n: i32) -> bool {
        if n == 1 {
            return true;
        }
        if n < 3 || n % 3 != 0 {
            return false;
        }
        Self::is_power_of_three(n / 3)
    }
}
```