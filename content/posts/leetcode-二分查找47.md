---
title: "leetcode-二分查找47"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 准时到达的列车最小时速

给你一个浮点数 hour ，表示你到达办公室可用的总通勤时间。要到达办公室，你必须按给定次序乘坐 n 趟列车。另给你一个长度为 n 的整数数组 dist ，其中 dist[i] 表示第 i 趟列车的行驶距离（单位是千米）。

每趟列车均只能在整点发车，所以你可能需要在两趟列车之间等待一段时间。

例如，第 1 趟列车需要 1.5 小时，那你必须再等待 0.5 小时，搭乘在第 2 小时发车的第 2 趟列车。
返回能满足你在时限前到达办公室所要求全部列车的 最小正整数 时速（单位：千米每小时），如果无法准时到达，则返回 -1 。

生成的测试用例保证答案不超过 107 ，且 hour 的 小数点后最多存在两位数字 。


```
impl Solution {
    pub fn min_speed_on_time(dist: Vec<i32>, hour: f64) -> i32 {
        let n = dist.len();
        // hour 小数点后最多两位，乘 100 转为整数，避免浮点误差
        let hr = (hour * 100.0).round() as i64;

        // 前 n-1 段每段至少耗时 1 小时（整点发车），因此总时间必须大于 n-1
        if hr <= (n as i64 - 1) * 100 {
            return -1;
        }

        // 判断速度 v 能否在时限内到达
        let feasible = |v: i64| -> bool {
            let mut time = 0i64;
            // 前 n-1 段需等待到整点，耗时向上取整
            for &d in &dist[..n - 1] {
                time += (d as i64 - 1) / v + 1;
            }
            // 最后一段无需等待，可精确计时
            // 比较 time + dist[n-1]/v <= hr/100，通分为整数
            let d_last = dist[n - 1] as i64;
            (time * v + d_last) * 100 <= hr * v
        };

        // 二分最小可行速度，答案不超过 1e7
        let mut lo = 1;
        let mut hi = 10_000_000;
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            if feasible(mid) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        lo as i32
    }
}
```
