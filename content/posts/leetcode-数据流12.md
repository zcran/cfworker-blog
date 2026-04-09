---
title: "leetcode-数据流12"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 序列顺序查询

一个观光景点由它的名字 name 和景点评分 score 组成，其中 name 是所有观光景点中 唯一 的字符串，score 是一个整数。景点按照最好到最坏排序。景点评分 越高 ，这个景点越好。如果有两个景点的评分一样，那么 字典序较小 的景点更好。

你需要搭建一个系统，查询景点的排名。初始时系统里没有任何景点。这个系统支持：

添加 景点，每次添加 一个 景点。
查询 已经添加景点中第 i 好 的景点，其中 i 是系统目前位置查询的次数（包括当前这一次）。
比方说，如果系统正在进行第 4 次查询，那么需要返回所有已经添加景点中第 4 好的。
注意，测试数据保证 任意查询时刻 ，查询次数都 不超过 系统中景点的数目。

请你实现 SORTracker 类：

SORTracker() 初始化系统。
void add(string name, int score) 向系统中添加一个名为 name 评分为 score 的景点。
string get() 查询第 i 好的景点，其中 i 是目前系统查询的次数（包括当前这次查询）。


```
use std::cmp::Ordering;
use std::collections::BTreeSet;

struct Scene {
    name: String,
    score: i32,
}

impl Eq for Scene {}

impl PartialEq<Self> for Scene {
    fn eq(&self, other: &Self) -> bool {
        self.name == other.name
    }
}

impl PartialOrd<Self> for Scene {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        if self.score == other.score {
            self.name.partial_cmp(&other.name)
        } else {
            other.score.partial_cmp(&self.score)
        }
    }
}

impl Ord for Scene {
    fn cmp(&self, other: &Self) -> Ordering {
        if self.score == other.score {
            self.name.cmp(&other.name)
        } else {
            other.score.cmp(&self.score)
        }
    }
}

#[derive(Default)]
struct SORTracker {
    bt_up: BTreeSet<Scene>,
    bt_down: BTreeSet<Scene>,
}

impl SORTracker {
    fn new() -> Self {
        Self {
            ..Default::default()
        }
    }

    fn add(&mut self, name: String, score: i32) {
        self.bt_up.insert(Scene { name, score });
        self.bt_down.insert(self.bt_up.pop_last().unwrap());
    }

    fn get(&mut self) -> String {
        if let Some(sc) = self.bt_down.pop_first() {
            let res = sc.name.clone();
            self.bt_up.insert(sc);
            res
        } else {
            String::new()
        }
    }
}
```


对顶堆对 SORTracker 的帮助

你的 SORTracker 问题本质上是：动态维护一个有序序列，并支持按顺序取出元素。

问题映射

需求                            	 对顶堆的对应
每次添加一个 (score, name)	         插入到合适位置
每次取出第 i 高的元素              	 维护两堆的大小关系
按分数降序、名称升序                	自定义比较器
