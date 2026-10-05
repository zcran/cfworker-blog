---
title: "leetcode-贪心35"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 视频拼接

你将会获得一系列视频片段，这些片段来自于一项持续时长为 time 秒的体育赛事。这些片段可能有所重叠，也可能长度不一。

使用数组 clips 描述所有的视频片段，其中 clips[i] = [starti, endi] 表示：某个视频片段开始于 starti 并于 endi 结束。

甚至可以对这些片段自由地再剪辑：

例如，片段 [0, 7] 可以剪切成 [0, 1] + [1, 3] + [3, 7] 三部分。

我们需要将这些片段进行再剪辑，并将剪辑后的内容拼接成覆盖整个运动过程的片段（[0, time]）。返回所需片段的最小数目，如果无法完成该任务，则返回 -1 。


```
impl Solution {
    pub fn video_stitching(mut clips: Vec<Vec<i32>>, time: i32) -> i32 {
        // 按起点升序排列，便于线性扫描
        clips.sort_unstable();

        let (mut count, mut cur_end, mut next_end) = (0, 0, 0);

        for clip in &clips {
            let (start, end) = (clip[0], clip[1]);

            // 出现无法被当前覆盖范围接上的片段，说明有断档
            if start > next_end {
                return -1;
            }

            // 当前片段超出已达范围，需要新增一段并推进覆盖边界
            if start > cur_end {
                count += 1;
                cur_end = next_end;
            }

            next_end = next_end.max(end);

            // 已经能覆盖整个时间区间
            if next_end >= time {
                return count + 1;
            }
        }

        -1
    }
}
```
