---
title: "leetcode-二分查找61"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 礼盒的最大甜蜜度

给你一个正整数数组 price ，其中 price[i] 表示第 i 类糖果的价格，另给你一个正整数 k 。

商店组合 k 类 不同 糖果打包成礼盒出售。礼盒的 甜蜜度 是礼盒中任意两种糖果 价格 绝对差的最小值。

返回礼盒的 最大 甜蜜度。


```
impl Solution {
    pub fn maximum_tastiness(mut price: Vec<i32>, k: i32) -> i32 {
        price.sort_unstable();

        // 判断甜蜜度至少为 d 时，能否选出 k 类糖果
        let can_pick = |d: i32| -> bool {
            let mut cnt = 1;
            let mut pre = price[0];
            for &p in &price[1..] {
                if p - pre >= d {
                    cnt += 1;
                    if cnt >= k {
                        return true;
                    }
                    pre = p;
                }
            }
            false
        };

        // 二分答案，区间为 [left, right)
        let mut left = 0;
        let mut right = (price.last().unwrap() - price[0]) / (k - 1) + 1;

        while left + 1 < right {
            let mid = left + (right - left) / 2;
            if can_pick(mid) {
                left = mid;
            } else {
                right = mid;
            }
        }

        left
    }
}
```
