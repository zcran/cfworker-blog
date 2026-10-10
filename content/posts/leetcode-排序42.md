---
title: "leetcode-排序42"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 堆叠长方体的最大高度

给你 n 个长方体 cuboids ，其中第 i 个长方体的长宽高表示为 cuboids[i] = [widthi, lengthi, heighti]（下标从 0 开始）。请你从 cuboids 选出一个 子集 ，并将它们堆叠起来。

如果 widthi <= widthj 且 lengthi <= lengthj 且 heighti <= heightj ，你就可以将长方体 i 堆叠在长方体 j 上。你可以通过旋转把长方体的长宽高重新排列，以将它放在另一个长方体上。

返回 堆叠长方体 cuboids 可以得到的 最大高度 。




```
impl Solution {
    /// 返回堆叠长方体可以得到的最大高度。
    ///
    /// 思路：
    /// 1. 每个长方体可旋转，因此先将其三边升序排序，这样只要三个维度分别不大于另一个长方体，就可堆叠。
    /// 2. 按三边之和升序排序所有长方体，保证可堆叠时较小的长方体一定排在前面。
    /// 3. 动态规划：dp[i] 表示以第 i 个长方体为最底部时的最大堆叠高度。
    ///    若第 j 个长方体（j < i）可以放在第 i 个长方体上，则 dp[i] = max(dp[i], dp[j] + height_i)。
    pub fn max_height(mut cuboids: Vec<Vec<i32>>) -> i32 {
        // 每个长方体三边升序，便于统一比较
        for cuboid in cuboids.iter_mut() {
            cuboid.sort_unstable();
        }

        // 按三边之和升序排序，保证堆叠关系单向
        cuboids.sort_unstable_by_key(|c| c[0] + c[1] + c[2]);

        let n = cuboids.len();
        let mut dp = vec![0; n];
        let mut ans = 0;

        for i in 0..n {
            // 以当前长方体为最底部，初始高度为其自身高度（最长边）
            dp[i] = cuboids[i][2];

            // 尝试将之前的某个长方体堆叠在当前长方体之上
            for j in 0..i {
                if cuboids[i][0] >= cuboids[j][0]
                    && cuboids[i][1] >= cuboids[j][1]
                    && cuboids[i][2] >= cuboids[j][2]
                {
                    dp[i] = dp[i].max(dp[j] + cuboids[i][2]);
                }
            }

            ans = ans.max(dp[i]);
        }

        ans
    }
}
```
