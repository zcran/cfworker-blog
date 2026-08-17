---
title: "leetcode-计数87"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计已测试设备

给你一个长度为 n 、下标从 0 开始的整数数组 batteryPercentages ，表示 n 个设备的电池百分比。

你的任务是按照顺序测试每个设备 i，执行以下测试操作：

· 如果 batteryPercentages[i] 大于 0：
    · 增加 已测试设备的计数。
    · 将下标 j 在 [i + 1, n - 1] 的所有设备的电池百分比减少 1，确保它们的电池百分比 不会低于 0 ，即 batteryPercentages[j] = max(0, batteryPercentages[j] - 1)。
    · 移动到下一个设备。

· 否则，移动到下一个设备而不执行任何测试。

返回一个整数，表示按顺序执行测试操作后 已测试设备 的数量。




```
impl Solution {
    pub fn count_tested_devices(battery_percentages: Vec<i32>) -> i32 {
        let mut tested = 0;

        // 每个设备最终实际衰减量等于在它之前测试成功的设备数量
        // 所以设备 i 的最终电量为 battery_percentages[i] - tested
        for &initial in &battery_percentages {
            if initial > tested {
                tested += 1;
            }
        }

        tested
    }
}
```
