---
title: "leetcode-脑经急转弯11"
date: 2026-03-24T20:55:57+08:00
tags: ["leetcode", "脑筋急转弯"]
draft: false
---


## 最小无法得到的或值

给你一个下标从 0 开始的整数数组 nums 。

如果存在一些整数满足 0 <= index1 < index2 < ... < indexk < nums.length ，得到 nums[index1] | nums[index2] | ... | nums[indexk] = x ，那么我们说 x 是 可表达的 。换言之，如果一个整数能由 nums 的某个子序列的或运算得到，那么它就是可表达的。

请你返回 nums 不可表达的 最小非零整数 。


```
use std::collections::HashSet;
impl Solution {
    pub fn min_impossible_or(nums: Vec<i32>) -> i32 {
        let hs:HashSet<i32> = nums.iter().map(|&x|x).collect();
        let mut i = 1;
        loop
        {
            if !hs.contains(&i){return i;}
            i <<= 1;
        }
    }
}

```


// 示例1
nums = [1, 2, 4, 8]
// 所有2的幂次都存在
// 但1,2,4,8都存在，可以组合出1-15
// 返回16？不对，实际上程序会检查1,2,4,8都包含
// 然后检查16(10000)不存在 → 返回16

// 示例2
nums = [1, 2, 4]
// 检查: 1存在, 2存在, 4存在
// 检查8(1000)不存在 → 返回8

为什么只检查2的幂次？

因为：

如果所有小于 2^k 的2的幂次都存在，就能通过OR组合出 2^k - 1 以内的所有数
如果 2^k 本身不存在，它就无法被任何OR运算得到
如果某个非2的幂次是最小不可达数，那么它一定包含某个不存在的2的幂次作为位

复杂度

时间复杂度：O(n + log(答案))
空间复杂度：O(n)
