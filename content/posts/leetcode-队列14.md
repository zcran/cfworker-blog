---
title: "leetcode-队列14"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 买票需要的时间


有 n 个人前来排队买票，其中第 0 人站在队伍 最前方 ，第 (n - 1) 人站在队伍 最后方 。

给你一个下标从 0 开始的整数数组 tickets ，数组长度为 n ，其中第 i 人想要购买的票数为 tickets[i] 。

每个人买票都需要用掉 恰好 1 秒 。一个人 一次只能买一张票 ，如果需要购买更多票，他必须走到  队尾 重新排队（瞬间 发生，不计时间）。如果一个人没有剩下需要买的票，那他将会 离开 队伍。

返回位于位置 k（下标从 0 开始）的人完成买票需要的时间（以秒为单位）。


```
impl Solution {
    /// 计算所有人买完票所需的总时间（第 k 个人买完即停止）
    ///
    /// # 算法思路
    /// - 第 k 个人需要买 `target` 张票，他买最后一张时，排队过程结束。
    /// - 对于他前面或同位置的人（i <= k），每人最多买 `target` 张；
    /// - 对于他后面的人（i > k），每人最多买 `target - 1` 张（因为最后一轮未轮到他）。
    /// - 总时间 = 对每个人取 `min(实际票数, 上限)` 并求和。
    pub fn time_required_to_buy(tickets: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;                // 转为索引类型
        let target = tickets[k];           // 第 k 个人需要买的票数
        let mut total = 0;

        for (i, &t) in tickets.iter().enumerate() {
            if i <= k {
                total += t.min(target);
            } else {
                total += t.min(target - 1); // target ≥ 1 时安全
            }
        }
        total
    }
}
```
