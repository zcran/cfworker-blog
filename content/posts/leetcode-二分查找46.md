---
title: "leetcode-二分查找46"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 有界数组中指定下标处的最大值

给你三个正整数 n、index 和 maxSum 。你需要构造一个同时满足下述所有条件的数组 nums（下标 从 0 开始 计数）：

nums.length == n
nums[i] 是 正整数 ，其中 0 <= i < n
abs(nums[i] - nums[i+1]) <= 1 ，其中 0 <= i < n-1
nums 中所有元素之和不超过 maxSum
nums[index] 的值被 最大化

返回你所构造的数组中的 nums[index] 。

注意：abs(x) 等于 x 的前提是 x >= 0 ；否则，abs(x) 等于 -x 。


```
impl Solution {
    pub fn max_value(n: i32, index: i32, max_sum: i32) -> i32 {
        let n = n as i64;
        let index = index as i64;
        let max_sum = max_sum as i64;

        // 计算从 peak 开始，向一侧延伸 cnt 个位置的最小总和（含 peak）
        // 序列形如：peak, peak-1, ..., 1, 1, ... （总长度 cnt）
        let side_sum = |peak: i64, cnt: i64| -> i64 {
            if peak >= cnt {
                // 不会降到 1 以下，直接等差数列求和
                (peak + peak - cnt + 1) * cnt / 2
            } else {
                // 降到 1 后，剩余位置全部为 1
                (peak + 1) * peak / 2 + (cnt - peak)
            }
        };

        // 判断以 peak 作为 nums[index] 时，整个数组的最小总和是否 <= max_sum
        let feasible = |peak: i64| -> bool {
            let left = side_sum(peak, index + 1);   // 左侧含 index，共 index+1 个位置
            let right = side_sum(peak, n - index);  // 右侧含 index，共 n-index 个位置
            left + right - peak <= max_sum          // 减去重复计算的 peak
        };

        // 二分最大可行的 peak
        let mut lo = 1;
        let mut hi = max_sum;
        while lo < hi {
            let mid = lo + (hi - lo + 1) / 2;
            if feasible(mid) {
                lo = mid;
            } else {
                hi = mid - 1;
            }
        }
        lo as i32
    }
}
```
