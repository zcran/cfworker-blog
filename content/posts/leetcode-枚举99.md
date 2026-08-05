---
title: "leetcode-枚举99"
date: 2026-07-09T10:05:04+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 可排序整数求和

给你一个长度为 n 的整数数组 nums。

如果一个整数 k 满足以下条件，则称其为 可排序整数：k 是 n 的 因数，且可以通过依次执行以下操作将 nums 排序为 非递减顺序：

将 nums 划分为长度为 k 的 连续子数组。

独立地对每个子数组进行循环移动（左移或右移任意次数）。

返回所有可能的可排序整数 k 的和。

子数组 是数组中的一个连续、非空元素序列。


```
impl Solution {
    pub fn sortable_integers(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut sum = 0;

        // 枚举所有因数 k
        for k in 1..=n {
            if n % k == 0 && Self::can_sort(&nums, k) {
                sum += k as i32;
            }
        }
        sum
    }

    /// 判断是否可以将 nums 按长度 k 分组，每组循环移位后整体非递减
    fn can_sort(nums: &[i32], k: usize) -> bool {
        let n = nums.len();

        // k == 1 时，无法改变顺序，必须原数组已有序
        if k == 1 {
            return nums.windows(2).all(|w| w[0] <= w[1]);
        }

        let mut prev_tail = None;
        for start in (0..n).step_by(k) {
            let end = (start + k).min(n);
            let group = &nums[start..end];

            // 获取该组循环移位后的有序序列的首尾（如果可能）
            if let Some((head, tail)) = Self::rotate_to_sorted(group) {
                // 检查与前一组尾部的衔接
                if let Some(pt) = prev_tail {
                    if pt > head {
                        return false;
                    }
                }
                prev_tail = Some(tail);
            } else {
                return false; // 该组无法通过循环移位变为有序
            }
        }

        true
    }

    /// 尝试将数组循环移位变为非递减序列，返回 (新首, 新尾)
    /// 如果不可能则返回 None
    fn rotate_to_sorted(group: &[i32]) -> Option<(i32, i32)> {
        let m = group.len();
        if m <= 1 {
            return Some((group[0], group[0]));
        }

        // 找到所有下降点（nums[i] > nums[i+1]）
        let mut breaks = Vec::new();
        for i in 0..m - 1 {
            if group[i] > group[i + 1] {
                breaks.push(i + 1); // 记录断点位置（下一个元素的索引）
            }
        }

        match breaks.len() {
            0 => {
                // 已经有序
                Some((group[0], group[m - 1]))
            }
            1 => {
                let p = breaks[0]; // 断点位置，旋转后首为 group[p]
                // 检查旋转后是否有序：
                // 条件：group[p]..group[m-1] 递增，group[0]..group[p-1] 递增，且 group[m-1] <= group[0]
                // 实际上只需检查 group[p-1] <= group[p]? 不，旋转后序列是 group[p], group[p+1], ..., group[m-1], group[0], ..., group[p-1]
                // 需要这些元素整体递增。等价于检查整个环上只有一个下降点，且最后一个 <= 第一个。
                // 由于我们已经知道只有一个断点，检查旋转后的序列是否递增即可：
                // 即从 p 到 m-1 递增，且从 0 到 p-1 递增，且 group[m-1] <= group[0]
                if group[m - 1] > group[0] {
                    return None;
                }
                // 检查 segment1: group[p..m-1] 递增
                for i in p..m - 1 {
                    if group[i] > group[i + 1] {
                        return None;
                    }
                }
                // 检查 segment2: group[0..p-1] 递增
                for i in 0..p - 1 {
                    if group[i] > group[i + 1] {
                        return None;
                    }
                }
                // 旋转后的首是 group[p]，尾是 group[p-1]
                Some((group[p], group[p - 1]))
            }
            _ => None, // 超过一个断点，无法一次旋转有序
        }
    }
}
```
