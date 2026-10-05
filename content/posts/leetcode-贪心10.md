---
title: "leetcode-贪心10"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 摆动序列

如果连续数字之间的差严格地在正数和负数之间交替，则数字序列称为 摆动序列 。第一个差（如果存在的话）可能是正数或负数。仅有一个元素或者含两个不等元素的序列也视作摆动序列。

例如， [1, 7, 4, 9, 2, 5] 是一个 摆动序列 ，因为差值 (6, -3, 5, -7, 3) 是正负交替出现的。

相反，[1, 4, 7, 2, 5] 和 [1, 7, 4, 5, 5] 不是摆动序列，第一个序列是因为它的前两个差值都是正数，第二个序列是因为它的最后一个差值为零。

子序列 可以通过从原始序列中删除一些（也可以不删除）元素来获得，剩下的元素保持其原始顺序。

给你一个整数数组 nums ，返回 nums 中作为 摆动序列 的 最长子序列的长度 。


```
impl Solution {
    pub fn wiggle_max_length(nums: Vec<i32>) -> i32 {
        // up:   以当前元素结尾、最后一段为上升的最长摆动子序列长度
        // down: 以当前元素结尾、最后一段为下降的最长摆动子序列长度
        let (mut up, mut down) = (1, 1);

        for w in nums.windows(2) {
            match w[0].cmp(&w[1]) {
                std::cmp::Ordering::Greater => down = down.max(up + 1),
                std::cmp::Ordering::Less => up = up.max(down + 1),
                std::cmp::Ordering::Equal => {}
            }
        }

        up.max(down)
    }
}
```
