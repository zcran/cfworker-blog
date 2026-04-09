---
title: "leetcode-数据流11"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 股票价格波动

给你一支股票价格的数据流。数据流中每一条记录包含一个 时间戳 和该时间点股票对应的 价格 。

不巧的是，由于股票市场内在的波动性，股票价格记录可能不是按时间顺序到来的。某些情况下，有的记录可能是错的。如果两个有相同时间戳的记录出现在数据流中，前一条记录视为错误记录，后出现的记录 更正 前一条错误的记录。

请你设计一个算法，实现：

更新 股票在某一时间戳的股票价格，如果有之前同一时间戳的价格，这一操作将 更正 之前的错误价格。
找到当前记录里 最新股票价格 。最新股票价格 定义为时间戳最晚的股票价格。
找到当前记录里股票的 最高价格 。
找到当前记录里股票的 最低价格 。
请你实现 StockPrice 类：

StockPrice() 初始化对象，当前无股票价格记录。
void update(int timestamp, int price) 在时间点 timestamp 更新股票价格为 price 。
int current() 返回股票 最新价格 。
int maximum() 返回股票 最高价格 。
int minimum() 返回股票 最低价格 。


```
use std::collections::{HashMap, BTreeMap};

struct StockPrice {
    cur: i32,
    time_price: HashMap<i32, i32>,
    price_cnt: BTreeMap<i32, i32>,
}

impl StockPrice {
    fn new() -> Self {
        Self {
            cur: 0,
            time_price: HashMap::new(),
            price_cnt: BTreeMap::new(),
        }
    }

    fn update(&mut self, timestamp: i32, price: i32) {
        self.cur = self.cur.max(timestamp);

        // 处理旧价格
        if let Some(&old) = self.time_price.get(&timestamp) {
            self.remove_price_count(old);
        }

        self.time_price.insert(timestamp, price);
        self.add_price_count(price);
    }

    fn remove_price_count(&mut self, price: i32) {
        if let Some(count) = self.price_cnt.get_mut(&price) {
            *count -= 1;
            if *count == 0 {
                self.price_cnt.remove(&price);
            }
        }
    }

    fn add_price_count(&mut self, price: i32) {
        *self.price_cnt.entry(price).or_insert(0) += 1;
    }

    fn current(&self) -> i32 {
        self.time_price[&self.cur]
    }

    fn maximum(&self) -> i32 {
        *self.price_cnt.iter().next_back().unwrap().0
    }

    fn minimum(&self) -> i32 {
        *self.price_cnt.iter().next().unwrap().0
    }
}
```
