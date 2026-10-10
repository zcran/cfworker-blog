---
title: "leetcode-动态规划159"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 跳跃游戏 IX

给你一个整数数组 nums。

从任意下标 i 出发，你可以根据以下规则跳跃到另一个下标 j：

仅当 nums[j] < nums[i] 时，才允许跳跃到下标 j，其中 j > i。
仅当 nums[j] > nums[i] 时，才允许跳跃到下标 j，其中 j < i。

对于每个下标 i，找出从 i 出发且可以跳跃 任意 次，能够到达 nums 中的 最大值 是多少。

返回一个数组 ans，其中 ans[i] 是从下标 i 出发可以到达的最大值。


```
impl Solution {
    pub fn max_value(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();

        // prev_max[i] = (前缀 nums[0..=i] 的最大值, 该最大值最靠右的下标)
        let mut prev_max = vec![(i32::MIN, 0usize); n];
        let mut cur = (i32::MIN, 0usize);
        for (i, &v) in nums.iter().enumerate() {
            if v > cur.0 {
                cur = (v, i);
            }
            prev_max[i] = cur;
        }

        let mut ans = vec![0i32; n];

        // 递归处理区间 [0, r]：
        // - pivot 为 [0, r] 中最大值的最右位置
        // - right_max 为右侧区间已确定的可达最大值
        // - right_min 为右侧区间所有元素的最小值
        //
        // 关键结论：
        //   对于 pivot 左侧的元素，只能向右跳到比它小的元素，
        //   因此它们能到达的最大值取 max(right_max, 左侧前缀最大值中比 right_min 小的部分)。
        //   pivot 处的元素本身为区间最大值，可达 right_max（若其大于右侧最小值）或自身。
        fn process(
            r: usize,
            right_min: i32,
            right_max: i32,
            nums: &[i32],
            prev_max: &[(i32, usize)],
            ans: &mut [i32],
        ) {
            let (p_max, pivot) = prev_max[r];
            // 当前区间可达的最大值
            let curr_max = if p_max <= right_min { p_max } else { right_max };

            // 更新右侧区间的最小值
            let mut next_right_min = right_min.min(p_max);
            for i in pivot..=r {
                ans[i] = curr_max;
                next_right_min = next_right_min.min(nums[i]);
            }

            if pivot == 0 {
                return;
            }

            // 递归处理左侧区间 [0, pivot-1]
            process(pivot - 1, next_right_min, curr_max, nums, prev_max, ans);
        }

        process(n - 1, i32::MAX, 0, &nums, &prev_max, &mut ans);

        ans
    }
}
```
