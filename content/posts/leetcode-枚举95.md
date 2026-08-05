---
title: "leetcode-枚举95"
date: 2026-07-09T10:05:04+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 移除至多一个元素后的最长交替子数组

给你一个整数数组nums。

如果一个子数组nums[l..r]满足以下条件之一，则称其为 交替子数组：

nums[l] < nums[l + 1] > nums[l + 2] < nums[l + 3] > ...
nums[l] > nums[l + 1] < nums[l + 2] > nums[l + 3] < ...

换句话说，如果我们比较子数组中的相邻元素，这些比较在严格大于和严格小于之间交替进行，则该子数组是交替的。

你可以从数组nums中最多移除一个元素。然后，你需要从nums中选择一个交替子数组。

返回一个整数，表示你可以选择的最长交替子数组的长度。

子数组 是数组中连续的一段元素。

长度为 1 的子数组被认为是交替的。


```
impl Solution {
    pub fn longest_alternating(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n == 1 {
            return 1;
        }

        // 计算以每个位置结尾的最长交替子数组长度
        let pre = Self::calc_ending(&nums);
        // 计算以每个位置开头的最长交替子数组长度
        let mut reversed = nums.clone();
        reversed.reverse();
        let mut suf = Self::calc_ending(&reversed);
        suf.reverse();

        // 不删除元素时的最长交替子数组
        let mut ans = *pre.iter().max().unwrap();

        // 枚举删除 nums[i]
        for i in 1..n - 1 {
            if nums[i - 1] == nums[i + 1] {
                continue; // 相等无法交替
            }

            // 比较相邻三个数的大小关系，用 -1, 0, 1 表示
            let x = if i > 1 {
                (nums[i - 2] > nums[i - 1]) as i8 - (nums[i - 2] < nums[i - 1]) as i8
            } else {
                0
            };
            let y = (nums[i - 1] > nums[i + 1]) as i8 - (nums[i - 1] < nums[i + 1]) as i8;
            let z = if i < n - 2 {
                (nums[i + 1] > nums[i + 2]) as i8 - (nums[i + 1] < nums[i + 2]) as i8
            } else {
                0
            };

            if x == -y && x == z {
                // 完全拼接：左右两边合并
                ans = ans.max(pre[i - 1] + suf[i + 1]);
            } else {
                if x == -y {
                    // 只拼接左边和删除的元素
                    ans = ans.max(pre[i - 1] + 1);
                }
                if z == -y {
                    // 只拼接删除的元素和右边
                    ans = ans.max(suf[i + 1] + 1);
                }
            }
        }

        ans
    }

    /// 计算以每个位置结尾的最长交替子数组长度
    fn calc_ending(a: &[i32]) -> Vec<i32> {
        let n = a.len();
        let mut f = vec![1; n];

        for i in 1..n {
            if a[i - 1] == a[i] {
                f[i] = 1;
            } else if i >= 2
                && a[i - 2] != a[i - 1]
                && ((a[i - 2] < a[i - 1]) as i8) == ((a[i - 1] > a[i]) as i8)
            {
                f[i] = f[i - 1] + 1;
            } else {
                f[i] = 2;
            }
        }

        f
    }
}
```
