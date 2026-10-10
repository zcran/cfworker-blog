---
title: "leetcode-排序69"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 无需开会的工作日

给你一个正整数 days，表示员工可工作的总天数（从第 1 天开始）。另给你一个二维数组 meetings，长度为 n，其中 meetings[i] = [start_i, end_i] 表示第 i 次会议的开始和结束天数（包含首尾）。

返回员工可工作且没有安排会议的天数。

注意：会议时间可能会有重叠。




```
impl Solution {
    pub fn count_days(days: i32, mut meetings: Vec<Vec<i32>>) -> i32 {
        // 按会议开始时间排序，便于合并重叠区间
        meetings.sort_unstable_by_key(|m| m[0]);

        let mut free = 0;      // 累计可工作天数
        let mut covered = 0;   // 当前已合并区间覆盖到的最后一天

        for m in meetings {
            let (start, end) = (m[0], m[1]);
            // 出现新区间（与当前区间不重叠），结算中间的空闲天数
            if start > covered + 1 {
                free += start - covered - 1;
            }
            // 扩展当前合并区间的右边界
            covered = covered.max(end);
        }

        // 结算最后一个合并区间之后剩余的空闲天数
        free += days - covered;

        free
    }
}
```
