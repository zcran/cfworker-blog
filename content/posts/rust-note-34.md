---
title: "Rust Note 34"
date: 2023-06-10T19:51:15+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 爬楼梯

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

### C 解法1：
[![pCVeCM6.png](https://s1.ax1x.com/2023/06/10/pCVeCM6.png)](https://imgse.com/i/pCVeCM6)
```
 // 通项公式法
class Solution {
public:
    int climbStairs(int n) {
        double sqrt5 = sqrt(5);
        double fibn = pow((1 + sqrt5) / 2, n + 1) - pow((1 - sqrt5) / 2, n + 1);
        return (int)round(fibn / sqrt5);
    }
};
```

### C解法2:
[![pCVeEIH.png](https://s1.ax1x.com/2023/06/10/pCVeEIH.png)](https://imgse.com/i/pCVeEIH)
```
 // 矩阵快速幂
class Solution {
public:
    vector<vector<long long>> multiply(vector<vector<long long>> &a, vector<vector<long long>> &b) {
        vector<vector<long long>> c(2, vector<long long>(2));
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                c[i][j] = a[i][0] * b[0][j] + a[i][1] * b[1][j];
            }
        }
        return c;
    }

    vector<vector<long long>> matrixPow(vector<vector<long long>> a, int n) {
        vector<vector<long long>> ret = {{1, 0}, {0, 1}};
        while (n > 0) {
            if ((n & 1) == 1) {
                ret = multiply(ret, a);
            }
            n >>= 1;
            a = multiply(a, a);
        }
        return ret;
    }

    int climbStairs(int n) {
        vector<vector<long long>> ret = {{1, 1}, {1, 0}};
        vector<vector<long long>> res = matrixPow(ret, n);
        return res[0][0];
    }
};
```


### Rust 解法：
```
impl Solution {
    pub fn climb_stairs(n: i32) -> i32 {
        let (mut a, mut b) = (1, 1);
        (0..n).for_each(|_| {b=a+b;a=b-a});
        a
    }
}
```