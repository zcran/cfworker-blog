---
title: "leetcode-二分查找94"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 考虑空闲时间的会议最大收益

给你一个二维整数数组 meetings，其中 meetings[i] = [starti, endi, revenuei] 表示一场会议从时间 starti 开始，在时间 endi 结束，并可获得 revenuei 的收益。

所有会议均采用 左闭右开区间 [start, end) 表示，因此仅在端点处相接的会议 不视为 重叠。

你可以选择任意一个会议 非空子集 ，所选会议两两不重叠。每选择一场会议，你都可以获得该会议对应的收益。

将所选会议按照 开始时间递增 的顺序排列。对于该顺序中每一对相邻会议，你还可以根据它们之间的空闲时间获得额外收益，每单位空闲时间获得 1 单位收益。空闲时间等于后一场会议的开始时间减去前一场会议的结束时间。

最早一场所选会议开始之前，以及最晚一场所选会议结束之后的空闲时间不会产生收益。如果只选择一场会议，则不会获得任何空闲时间收益。

返回可以获得的 最大总收益 。

数组的 子集 是从数组中选择若干元素得到的集合。


```
impl Solution {
    pub fn max_earnings(mut meetings: Vec<Vec<i32>>) -> i64 {
        // 按结束时间从小到大排序
        meetings.sort_unstable_by_key(|m| m[1]);

        let n = meetings.len();
        let end0 = meetings[0][1] as i64;

        // pre_max[i+1] = 前 i+1 场会议中 (f[j] - end[j]) 的最大值
        let mut pre_max = vec![i64::MIN; n + 1];
        let mut ans = 0_i64;

        for i in 0..n {
            let start = meetings[i][0] as i64;
            let end = meetings[i][1] as i64;
            let revenue = meetings[i][2] as i64;

            let mut f = revenue;
            // 如果左边存在不重叠的会议，加上最优前缀贡献
            if start >= end0 {
                // 找到最后一个 end <= start 的会议，其对应下标为 j（0-based）
                let j = meetings.partition_point(|m| (m[1] as i64) <= start);
                // pre_max[j] 对应前 j 场会议，恰好是 end <= start 的部分
                f += pre_max[j] + start;
            }
            ans = ans.max(f);

            pre_max[i + 1] = pre_max[i].max(f - end);
        }

        ans
    }
}
```
