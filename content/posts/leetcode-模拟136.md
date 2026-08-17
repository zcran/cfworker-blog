---
title: "leetcode-模拟136"
date: 2026-08-08T11:31:17+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 切换打开灯泡

给你一个整数数组 bulbs，其中每个元素的取值范围为 1 到 100。

有 100 个电灯泡，按从 1 到 100 编号，初始时所有灯泡均为关闭状态。

对于数组 bulbs 中的每一个元素 bulbs[i]，执行以下操作：

如果第 bulbs[i] 个灯泡当前是关闭状态，将其打开。
如果第 bulbs[i] 个灯泡当前是打开状态，将其关闭。

返回一个整数列表，表示最终处于打开状态的灯泡编号，按升序排列。如果没有灯泡是打开的，返回一个空列表。




```
impl Solution {
    /// 模拟灯泡开关，返回最终打开的灯泡编号（升序）
    ///
    /// 优化点：
    /// - 固定大小数组 [bool; 100] 替代哈希表，零哈希开销、零内存分配
    /// - 编号范围 1-100 天然有序，遍历收集即得升序结果，无需排序
    /// - 预分配精确容量，避免 Vec 扩容
    pub fn toggle_light_bulbs(bulbs: Vec<i32>) -> Vec<i32> {
        let mut state = [false; 100];

        for &b in &bulbs {
            let idx = (b - 1) as usize; // 编号 1-100 映射到索引 0-99
            state[idx] = !state[idx];
        }

        let mut ans = Vec::with_capacity(100);
        for (idx, &on) in state.iter().enumerate() {
            if on {
                ans.push((idx + 1) as i32); // 索引 0-99 映射回编号 1-100
            }
        }

        ans
    }
}
```
