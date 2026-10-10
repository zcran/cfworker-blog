---
title: "leetcode-数组65"
date: 2026-10-10T20:38:45+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 数组中的有效元素

给你一个整数数组 nums。

如果元素 nums[i] 满足以下 至少一个 条件，则认为它是 有效 元素：

它 严格大于 其左侧的所有元素。
它 严格大于 其右侧的所有元素。

第一个元素和最后一个元素始终有效。

返回所有有效元素组成的数组，顺序与它们在 nums 中出现的顺序相同。


```
impl Solution {
    pub fn find_valid_elements(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        // right_valid[i] 表示 nums[i] 严格大于其右侧所有元素
        let mut right_valid = vec![false; n];
        let mut mx = i32::MIN;
        for i in (0..n).rev() {
            right_valid[i] = nums[i] > mx;
            mx = mx.max(nums[i]);
        }

        // 从左到右收集：严格大于左侧所有元素 或 严格大于右侧所有元素
        let mut ans = Vec::with_capacity(n);
        let mut mx = i32::MIN;
        for i in 0..n {
            if nums[i] > mx || right_valid[i] {
                ans.push(nums[i]);
            }
            mx = mx.max(nums[i]);
        }
        ans
    }
}
```
