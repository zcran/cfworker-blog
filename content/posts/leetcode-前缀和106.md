---
title: "leetcode-前缀和106"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 零数组变换 I

给定一个长度为 n 的整数数组 nums 和一个二维数组 queries，其中 queries[i] = [li, ri]。

对于每个查询 queries[i]：

在 nums 的下标范围 [li, ri] 内选择一个下标 子集。

将选中的每个下标对应的元素值减 1。

零数组 是指所有元素都等于 0 的数组。

如果在按顺序处理所有查询后，可以将 nums 转换为 零数组 ，则返回 true，否则返回 false。


```
impl Solution {
    pub fn is_zero_array(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> bool {
        let n = nums.len();
        let mut diff = vec![0; n + 1];

        // 差分数组：每个查询覆盖 [l, r] 区间
        for q in queries {
            let l = q[0] as usize;
            let r = q[1] as usize;
            diff[l] += 1;
            diff[r + 1] -= 1;
        }

        // 前缀和得到每个位置被覆盖的次数
        let mut ops = 0;
        for i in 0..n {
            ops += diff[i];
            if ops < nums[i] {
                return false;
            }
        }

        true
    }
}
```
