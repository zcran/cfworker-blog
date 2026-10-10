---
title: "leetcode-动态规划149"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 零数组变换 IV

给你一个长度为 n 的整数数组 nums 和一个二维数组 queries ，其中 queries[i] = [li, ri, vali]。

每个 queries[i] 表示以下操作在 nums 上执行：

从数组 nums 中选择范围 [li, ri] 内的一个下标子集。
将每个选中下标处的值减去 正好 vali。

零数组 是指所有元素都等于 0 的数组。

返回使得经过前 k 个查询（按顺序执行）后，nums 转变为 零数组 的最小可能 非负 值 k。如果不存在这样的 k，返回 -1。

数组的 子集 是指从数组中选择的一些元素（可能为空）。


```
use std::collections::HashSet;

impl Solution {
    pub fn min_zero_array(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> i32 {
        let n = nums.len();

        // 对每个下标 i，收集所有覆盖它的查询 (查询序号 j, 减去的值 val)
        let mut candidates = vec![Vec::new(); n];
        for (j, q) in queries.iter().enumerate() {
            let (l, r, val) = (q[0] as usize, q[1] as usize, q[2]);
            for i in l..=r {
                candidates[i].push((j, val));
            }
        }

        let mut ans = 0;

        for (i, &x) in nums.iter().enumerate() {
            if x == 0 {
                continue;
            }

            // reachable：用前若干查询能得到的、正整数中间值集合
            let mut reachable: HashSet<i32> = HashSet::new();
            reachable.insert(x);

            let mut min_k = -1;

            for &(j, val) in &candidates[i] {
                if reachable.contains(&val) {
                    // 已经能构造出 val，再执行该查询即可减到 0
                    min_k = j as i32 + 1;
                    break;
                }

                // 对所有可达值尝试减去 val，得到新的可达值
                let snapshot: Vec<i32> = reachable.iter().copied().collect();
                for seen in snapshot {
                    if seen >= val {
                        reachable.insert(seen - val);
                    }
                }
            }

            if min_k < 0 {
                return -1;
            }
            ans = ans.max(min_k);
        }

        ans
    }
}

```
