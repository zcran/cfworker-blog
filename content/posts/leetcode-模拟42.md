---
title: "leetcode-模拟42"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 平均等待时间

有一个餐厅，只有一位厨师。你有一个顾客数组 customers ，其中 customers[i] = [arrivali, timei] ：

arrivali 是第 i 位顾客到达的时间，到达时间按 非递减 顺序排列。

timei 是给第 i 位顾客做菜需要的时间。

当一位顾客到达时，他将他的订单给厨师，厨师一旦空闲的时候就开始做这位顾客的菜。每位顾客会一直等待到厨师完成他的订单。厨师同时只能做一个人的订单。厨师会严格按照 订单给他的顺序 做菜。

请你返回所有顾客需要等待的 平均 时间。与标准答案误差在 10-5 范围以内，都视为正确结果。


```
impl Solution {
    pub fn average_waiting_time(customers: Vec<Vec<i32>>) -> f64 {
        let mut finish = 0i64;      // 厨师完成当前订单的时间
        let mut total = 0i64;       // 所有顾客等待时间的总和

        for c in &customers {
            let arrival = c[0] as i64;
            let time = c[1] as i64;
            // 厨师开始时间 = max(完成上一单的时间, 顾客到达时间)
            // 完成时间 = 开始时间 + 做菜时间
            finish = finish.max(arrival) + time;
            // 等待时间 = 完成时间 - 到达时间
            total += finish - arrival;
        }

        total as f64 / customers.len() as f64
    }
}
```
