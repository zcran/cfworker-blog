---
title: "leetcode-设计22"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 设计停车系统

请你给一个停车场设计一个停车系统。停车场总共有三种不同大小的车位：大，中和小，每种尺寸分别有固定数目的车位。

请你实现 ParkingSystem 类：

ParkingSystem(int big, int medium, int small) 初始化 ParkingSystem 类，三个参数分别对应每种停车位的数目。

bool addCar(int carType) 检查是否有 carType 对应的停车位。 carType 有三种类型：大，中，小，分别用数字 1， 2 和 3 表示。一辆车只能停在  carType 对应尺寸的停车位中。如果没有空车位，请返回 false ，否则将该车停入车位并返回 true 。


```
/// 停车系统
///
/// 使用固定长度数组存储三种车位的剩余数量
/// 索引 1: 大车位, 2: 中车位, 3: 小车位
/// 时间复杂度: O(1), 空间复杂度: O(1)
struct ParkingSystem([i32; 4]);

impl ParkingSystem {
    /// 初始化停车系统
    ///
    /// # 参数
    /// - `big`: 大车位数量
    /// - `medium`: 中车位数量
    /// - `small`: 小车位数量
    fn new(big: i32, medium: i32, small: i32) -> Self {
        ParkingSystem([0, big, medium, small])
    }

    /// 添加车辆
    ///
    /// # 参数
    /// - `car_type`: 车辆类型 (1: 大, 2: 中, 3: 小)
    ///
    /// # 返回
    /// - `true`: 有空位并成功停车
    /// - `false`: 无对应空位
    fn add_car(&mut self, car_type: i32) -> bool {
        let idx = car_type as usize;
        if self.0[idx] > 0 {
            self.0[idx] -= 1;
            true
        } else {
            false
        }
    }
}
```
