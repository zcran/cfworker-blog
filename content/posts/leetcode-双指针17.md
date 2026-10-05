---
title: "leetcode-双指针17"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 快乐数

编写一个算法来判断一个数 n 是不是快乐数。

「快乐数」 定义为：

对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
然后重复这个过程直到这个数变为 1，也可能是 无限循环 但始终变不到 1。
如果这个过程 结果为 1，那么这个数就是快乐数。

如果 n 是 快乐数 就返回 true ；不是，则返回 false 。


```
impl Solution {
    /// 判断一个数是否为快乐数（快慢指针法）
    /// 时间复杂度 O(log n)，空间复杂度 O(1)
    /// 使用弗洛伊德判圈算法检测循环，避免使用 HashSet
    pub fn is_happy(n: i32) -> bool {
        let mut slow = n;
        let mut fast = next(n);

        while fast != 1 && slow != fast {
            slow = next(slow);
            fast = next(next(fast));
        }

        fast == 1
    }
}

/// 计算一个数各位数字的平方和
fn next(mut n: i32) -> i32 {
    let mut sum = 0;
    while n > 0 {
        let digit = n % 10;
        sum += digit * digit;
        n /= 10;
    }
    sum
}
```
