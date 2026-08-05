---
title: "leetcode-设计18"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 设计地铁系统

地铁系统跟踪不同车站之间的乘客出行时间，并使用这一数据来计算从一站到另一站的平均时间。

实现 UndergroundSystem 类：

· void checkIn(int id, string stationName, int t)
    · 通行卡 ID 等于 id 的乘客，在时间 t ，从 stationName 站进入
    · 乘客一次只能从一个站进入

· void checkOut(int id, string stationName, int t)
    · 通行卡 ID 等于 id 的乘客，在时间 t ，从 stationName 站离开

· double getAverageTime(string startStation, string endStation)
    · 返回从 startStation 站到 endStation 站的平均时间
    · 平均时间会根据截至目前所有从 startStation 站 直接 到达 endStation 站的行程进行计算，也就是从 startStation 站进入并从 endStation 离开的行程
    · 从 startStation 到 endStation 的行程时间与从 endStation 到 startStation 的行程时间可能不同
    · 在调用 getAverageTime 之前，至少有一名乘客从 startStation 站到达 endStation 站

你可以假设对 checkIn 和 checkOut 方法的所有调用都是符合逻辑的。如果一名乘客在时间 t1 进站、时间 t2 出站，那么 t1 < t2 。所有时间都按时间顺序发生。


```
use std::collections::HashMap;

/// 地铁系统：跟踪乘客行程并计算平均时间
struct UndergroundSystem {
    /// 行程数据：起点站 -> (终点站 -> (总时间, 次数))
    travel_data: HashMap<String, HashMap<String, (i64, i32)>>,
    /// 当前在途乘客：id -> (起点站, 上车时间)
    active_passengers: HashMap<i32, (String, i32)>,
}

impl UndergroundSystem {
    /// 创建一个新的地铁系统
    pub fn new() -> Self {
        Self {
            travel_data: HashMap::new(),
            active_passengers: HashMap::new(),
        }
    }

    /// 乘客进站
    ///
    /// # 参数
    /// - `id`: 乘客唯一标识
    /// - `station_name`: 进站名称
    /// - `t`: 进站时间
    pub fn check_in(&mut self, id: i32, station_name: String, t: i32) {
        self.active_passengers.insert(id, (station_name, t));
    }

    /// 乘客出站
    ///
    /// # 参数
    /// - `id`: 乘客唯一标识
    /// - `station_name`: 出站名称
    /// - `t`: 出站时间
    pub fn check_out(&mut self, id: i32, station_name: String, t: i32) {
        // 移除乘客并获取其进站信息
        if let Some((start_station, start_t)) = self.active_passengers.remove(&id) {
            let travel_time = (t - start_t) as i64;

            // 更新行程数据
            let destination_map = self.travel_data.entry(start_station).or_insert_with(HashMap::new);
            let (total_time, count) = destination_map
                .entry(station_name)
                .or_insert((0, 0));
            *total_time += travel_time;
            *count += 1;
        }
    }

    /// 计算从起点站到终点站的平均行程时间
    ///
    /// # 参数
    /// - `start_station`: 起点站
    /// - `end_station`: 终点站
    ///
    /// # 返回
    /// 平均行程时间（浮点数）
    ///
    /// # 前提条件
    /// 至少有一名乘客完成了该路线
    pub fn get_average_time(&self, start_station: String, end_station: String) -> f64 {
        let total_time = self.travel_data
            .get(&start_station)
            .and_then(|dest_map| dest_map.get(&end_station))
            .unwrap_or(&(0, 0));

        total_time.0 as f64 / total_time.1 as f64
    }
}
```
