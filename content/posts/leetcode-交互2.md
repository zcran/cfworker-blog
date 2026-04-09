---
title: "leetcode-交互2"
date: 2026-03-31T20:54:46+08:00
tags: ["leetcode", "交互"]
draft: false
---


## 猜数字大小

我们正在玩猜数字游戏。猜数字游戏的规则如下：

我会从 1 到 n 随机选择一个数字。 请你猜选出的是哪个数字。（我选的数字在整个游戏中保持不变）。

如果你猜错了，我会告诉你，我选出的数字比你猜测的数字大了还是小了。

你可以通过调用一个预先定义好的接口 int guess(int num) 来获取猜测结果，返回值一共有三种可能的情况：

-1：你猜的数字比我选出的数字大 （即 num > pick）。
1：你猜的数字比我选出的数字小 （即 num < pick）。
0：你猜的数字与我选出的数字相等。（即 num == pick）。
返回我选出的数字。


```
impl Solution {
    unsafe fn guessNumber(n: i32) -> i32 {
        let mut L = 0;
        let mut R = n;
        while L <= R {
            let mid = L + ((R - L) / 2);
            if  guess(mid) == 0 {
                return mid;
            } else if (guess(mid) == 1) {
                L = mid + 1;
            } else {
                R = mid - 1;
            }
        }
        L
    }
}

```
