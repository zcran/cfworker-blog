---
title: "leetcode-贪心174"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 字典序最大的 MEX 数组

给你一个整数数组 nums。

你需要构造一个数组 result，具体做法是重复执行以下操作，直到 nums 变为空：

· 选择一个整数 k，满足 1 <= k <= len(nums)。
· 计算 nums 的前 k 个元素的 MEX。
· 将这个 MEX 附加到 result。
· 从 nums 中移除前 k 个元素。

返回执行这些操作后能得到的 字典序最大 的数组 result。

数组的 MEX 是指数组中不包含的 最小非负 整数。

如果两个数组 a 和 b 在第一个不同的下标处，数组 a 的元素大于数组 b 的对应元素，则数组 a 字典序大于 数组 b。如果前 min(a.length, b.length) 个元素都相同，那么较长的数组是 字典序更大 的数组。


```
impl Solution {
    pub fn maximum_mex(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();

        // 1. 后缀 MEX：suf[i] = nums[i..] 的 MEX
        //    MEX 最大不超过 n（数组长度），seen 开 n+2 足够。
        let mut suf = vec![0i32; n];
        let mut seen = vec![false; n + 2];
        let mut mex = 0usize;
        for i in (0..n).rev() {
            let x = nums[i] as usize;
            if x < seen.len() {
                seen[x] = true;
            }
            while mex < seen.len() && seen[mex] {
                mex += 1;
            }
            suf[i] = mex as i32;
        }

        // 2. 贪心构造：每次取当前能得到的最大 MEX
        let mut ans = Vec::new();
        // 复用缓冲区，避免反复分配
        let mut cur = vec![false; n + 1];
        let mut i = 0;

        while i < n {
            let target = suf[i] as usize;

            // 后缀 MEX 为 0：没有 0，单取一个元素即可得到 MEX 0
            if target == 0 {
                ans.push(0);
                i += 1;
                continue;
            }

            // 向右扩展，直到前缀包含 0..target-1
            let mut cmex = 0usize;
            while cmex < target {
                let x = nums[i] as usize;
                if x < target {
                    cur[x] = true;
                }
                while cmex < target && cur[cmex] {
                    cmex += 1;
                }
                i += 1;
            }
            ans.push(target as i32);

            // 重置本段用到的标记，供下一段复用
            for c in cur[..target].iter_mut() {
                *c = false;
            }
        }

        ans
    }
}
```
