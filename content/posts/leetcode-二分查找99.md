---
title: "leetcode-二分查找99"
date: 2026-09-28T12:29:17+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 库存管理 I

仓库管理员以数组 stock 形式记录商品库存表。stock[i] 表示商品 id，可能存在重复。原库存表按商品 id 升序排列。现因突发情况需要进行商品紧急调拨，管理员将这批商品 id 提前依次整理至库存表最后。请你找到并返回库存表中编号的 最小的元素 以便及时记录本次调拨。


```
impl Solution {
    pub fn inventory_management(stock: Vec<i32>) -> i32 {
        let (mut lo, mut hi) = (0, stock.len() - 1);
        // 二分查找旋转数组的最小值
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            if stock[mid] > stock[hi] {
                // 最小值在右半部分
                lo = mid + 1;
            } else if stock[mid] < stock[hi] {
                // 最小值在左半部分（含 mid）
                hi = mid;
            } else {
                // 相等时无法判断，保守缩小右边界
                hi -= 1;
            }
        }
        stock[lo]
    }
}
```
