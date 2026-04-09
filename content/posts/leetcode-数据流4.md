---
title: "leetcode-数据流4"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 股票价格跨度

设计一个算法收集某些股票的每日报价，并返回该股票当日价格的 跨度 。

当日股票价格的 跨度 被定义为股票价格小于或等于今天价格的最大连续日数（从今天开始往回数，包括今天）。

例如，如果未来 7 天股票的价格是 [100,80,60,70,60,75,85]，那么股票跨度将是 [1,1,1,2,1,4,6] 。

实现 StockSpanner 类：

StockSpanner() 初始化类对象。
int next(int price) 给出今天的股价 price ，返回该股票当日价格的 跨度 。


```
struct StockSpanner {
    stack: Vec<(i32, i32)>,
}

impl StockSpanner {
    fn new() -> Self {
        Self { stack: Vec::new() }
    }

    fn next(&mut self, price: i32) -> i32 {
        let span = 1 + self.stack
            .iter()       // 创建栈的不可变迭代器
            .rev()        // 反转迭代器方向。从栈顶（最后一个元素）开始向前遍历。因为栈顶是最近添加的元素
            .take_while(|&&(p, _)| p <= price)        // 条件为真时持续取值，遇到假的就停止。第一个 &：表示引用（来自迭代器）。第二个 &：解引用元组。(p, _)：忽略第二个字段（跨度）
            .map(|&(_, s)| s)       // map：转换每个元素。|&(_, s)|：忽略价格，只取跨度。 s：返回跨度的值
            .sum::<i32>();      // 将所有跨度求和

        // 简洁的清理逻辑  返回栈顶元素的引用（最后一个元素）
        // |&(p, _)|：解引用元组，取价格字段
        while self.stack.last().map_or(false, |&(p, _)| p <= price) {
            self.stack.pop();
        }

        self.stack.push((price, span));   将当前价格和计算出的跨度组成的元组压入栈顶。保持栈的单调递减性质
        span
    }
}
```
