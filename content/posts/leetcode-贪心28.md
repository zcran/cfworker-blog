---
title: "leetcode-贪心28"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 删列造序 II

给定由 n 个字符串组成的数组 strs，其中每个字符串长度相等。

选取一个删除索引序列，对于 strs 中的每个字符串，删除对应每个索引处的字符。

比如，有 strs = ["abcdef", "uvwxyz"]，删除索引序列 {0, 2, 3}，删除后 strs 为["bef", "vyz"]。

假设，我们选择了一组删除索引 answer，那么在执行删除操作之后，最终得到的数组的元素是按 字典序（strs[0] <= strs[1] <= strs[2] ... <= strs[n - 1]）排列的，然后请你返回 answer.length 的最小可能值。




```
impl Solution {
    pub fn min_deletion_size(strs: Vec<String>) -> i32 {
        let (n, m) = (strs.len(), strs[0].len());
        // 以字节表示，便于按列快速索引
        let cols: Vec<&[u8]> = strs.iter().map(|s| s.as_bytes()).collect();

        // sorted[i] = true 表示第 i 与 i+1 行在前面的列比较中已经分出严格大小，
        // 后续列无需再检查它们是否有序
        let mut sorted = vec![false; n - 1];
        let mut deletions = 0;

        for j in 0..m {
            // 检查当前列在不破坏已确定顺序的前提下能否保留
            let mut can_keep = true;
            for i in 0..n - 1 {
                if !sorted[i] && cols[i][j] > cols[i + 1][j] {
                    can_keep = false;
                    break;
                }
            }

            if !can_keep {
                deletions += 1;
                continue;
            }

            // 保留该列后，更新已确定严格有序的相邻行对
            for i in 0..n - 1 {
                if !sorted[i] && cols[i][j] < cols[i + 1][j] {
                    sorted[i] = true;
                }
            }
        }

        deletions
    }
}
```
