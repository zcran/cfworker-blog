---
title: "Rust Note 41"
date: 2023-06-12T10:35:05+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 计数质数

给定整数 n ，返回 所有小于非负整数 n 的质数的数量 。

### C 解法：
解题思路：欧拉筛【时间复杂度O(nlgnlgn)】

埃氏筛
假定我们要求100以内的质数，我们从2开始遍历。
遍历到2,2是质数，于是我们把2的所有倍数全部删除，比如4、6、8、10......
遍历到3,3是质数，于是我们把3的所有倍数全都删除，比如6、9、12、15......
遍历到4,4是合数已经删除,继续遍历，以此类推即可找到范围内所有的素数
欧拉筛
欧拉筛是埃氏筛的改进版本，由于某些合数会有很多质因数，因此在删除的过程中会重复的删除，比如6，遍历到2时会进行删除，遍历到3时也会进行删除，所以为了避免这样的开销，欧拉筛将筛选出来的质数进行记录，在删除的过程中，只通过数的最小质因数筛数。
0、1既不是质数，也不是合数。
一个数，如果只有1和它本身两个约数，这样的数叫质数或素数。一个数，如果除了1和它本身还有别的约数，这样的数叫合数。而0的约数只有0，所以既不是质数又不是合数。如果1是质数，那就要有两个因数：1=1×1；如果1是合数，那就要有三个及以上的因数：1×1×1×1……化简之后就是1=1，只有一个因数，因此，1既不是质数也不是合数。
[![pCZr7vR.png](https://s1.ax1x.com/2023/06/12/pCZr7vR.png)](https://imgse.com/i/pCZr7vR)
```
 // 枚举
class Solution {
public:
    bool isPrime(int x) {
        for (int i = 2; i * i <= x; ++i) {
            if (x % i == 0) {
                return false;
            }
        }
        return true;
    }

    int countPrimes(int n) {
        int ans = 0;
        for (int i = 2; i < n; ++i) {
            ans += isPrime(i);
        }
        return ans;
    }
};
```


### Rust 解法：
```
impl Solution {
    pub fn count_primes(n: i32) -> i32 {
        if n <= 2 {
            return 0;
        }
        let n = n as usize;
        let mut record = vec![false;n/2];
        let mut count = 1;
        for i in (3..n).step_by(2) {
            let idx = i/2;
            if record[idx] {
                continue;
            }
            record[idx] = true;
            count += 1;
            let iter = (1..)
                .step_by(2)
                .map(|p| p*i)
                .take_while(|p| *p < n)
                .map(|p| p/2);
            for j in iter {
                record[j] = true;
            }
        }
        count
    }
}
```