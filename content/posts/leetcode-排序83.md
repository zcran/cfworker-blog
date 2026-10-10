---
title: "leetcode-排序83"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 移除不同值重排数组

给你一个整数数组 nums。

初始时，你有一个 空 数组 ans。重复执行以下操作，直到 nums 变为 空 ：

找出当前 nums 中 所有不同 的值。
将当前 nums 中每个 不同 的值各移除一个，并按 升序 将这些值依次添加到 ans 中。

返回数组 ans。


```
impl Solution {
    pub fn rearrange_array(nums: Vec<i32>) -> Vec<i32> {
        // cnt[x] 记录 x 已经出现的次数（用于确定它应放入第几层）
        let max_val = *nums.iter().max().unwrap() as usize;
        let mut cnt = vec![0usize; max_val + 1];

        // levels[c] 存放第 c 轮被移除的所有值
        let mut levels: Vec<Vec<i32>> = vec![Vec::new(); nums.len()];

        for &x in &nums {
            let c = cnt[x as usize];
            levels[c].push(x);
            cnt[x as usize] += 1;
        }

        // 每层内升序，再按层顺序拼接
        levels
            .into_iter()
            .flat_map(|mut level| {
                level.sort_unstable();
                level
            })
            .collect()
    }
}
```
