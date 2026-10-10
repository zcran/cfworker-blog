---
title: "leetcode-排序36"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 警告一小时内使用相同员工卡大于等于三次的人

力扣公司的员工都使用员工卡来开办公室的门。每当一个员工使用一次他的员工卡，安保系统会记录下员工的名字和使用时间。如果一个员工在一小时时间内使用员工卡的次数大于等于三次，这个系统会自动发布一个 警告 。

给你字符串数组 keyName 和 keyTime ，其中 [keyName[i], keyTime[i]] 对应一个人的名字和他在 某一天 内使用员工卡的时间。

使用时间的格式是 24小时制 ，形如 "HH:MM" ，比方说 "23:51" 和 "09:49" 。

请你返回去重后的收到系统警告的员工名字，将它们按 字典序升序 排序后返回。

请注意 "10:00" - "11:00" 视为一个小时时间范围内，而 "22:51" - "23:52" 不被视为一小时时间范围内。


```
use std::collections::HashMap;

impl Solution {
    /// 找出「一小时内使用员工卡 ≥ 3 次」的员工姓名，字典序升序返回
    ///
    /// 思路：先按姓名聚合所有使用时间，将 "HH:MM" 转为当天分钟数；
    /// 每个姓名的时间排序后，用长度为 3 的滑动窗口检查是否存在
    /// 窗口内最大最小差 ≤ 60 分钟（即同一小时内 3 次）。
    /// 时间 O(n log n)，额外空间 O(n)。
    pub fn alert_names(key_name: Vec<String>, key_time: Vec<String>) -> Vec<String> {
        // name -> 该员工所有使用时间（当天分钟数）
        let mut by_name: HashMap<&str, Vec<u32>> = HashMap::new();

        for (name, time) in key_name.iter().zip(&key_time) {
            // "HH:MM" -> 分钟数；split_once 避免额外分配 Vec
            let (h, m) = time.split_once(':').unwrap();
            let minutes = h.parse::<u32>().unwrap() * 60 + m.parse::<u32>().unwrap();
            by_name.entry(name).or_default().push(minutes);
        }

        let mut ans = Vec::new();
        for (name, times) in by_name.iter_mut() {
            if times.len() < 3 {
                continue;
            }
            times.sort_unstable();
            // 窗口长度 3：只要存在 times[j+2] - times[j] ≤ 60 即触发警告
            if times.windows(3).any(|w| w[2] - w[0] <= 60) {
                ans.push((*name).to_string());
            }
        }

        ans.sort_unstable();
        ans
    }
}
```
