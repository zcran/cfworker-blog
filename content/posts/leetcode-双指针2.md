---
title: "leetcode-双指针2"
date: 2026-09-05T09:53:00+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 盛最多水的容器

给定一个长度为 n 的整数数组 height 。有 n 条垂线，第 i 条线的两个端点是 (i, 0) 和 (i, height[i]) 。

找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

说明：你不能倾斜容器。


```
impl Solution {
    /// 双指针法求容器最大盛水量
    /// 时间复杂度 O(n)，空间复杂度 O(1)
    /// 贪心策略：每次移动较短的边，因为较短的边是当前容量的瓶颈
    pub fn max_area(height: Vec<i32>) -> i32 {
        let mut left = 0;
        let mut right = height.len() - 1;
        let mut max_water = 0;

        while left < right {
            // 当前容器的容量 = 宽度 × 最小高度
            let water = (right - left) as i32 * height[left].min(height[right]);
            max_water = max_water.max(water);

            // 移动较短的那条边，以期找到更高的边界
            if height[left] < height[right] {
                left += 1;
            } else {
                right -= 1;
            }
        }

        max_water
    }
}
```
