---
title: "leetcode-模拟21"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 查询后的偶数和

给出一个整数数组 A 和一个查询数组 queries。

对于第 i 次查询，有 val = queries[i][0], index = queries[i][1]，我们会把 val 加到 A[index] 上。然后，第 i 次查询的答案是 A 中偶数值的和。

（此处给定的 index = queries[i][1] 是从 0 开始的索引，每次查询都会永久修改数组 A。）

返回所有查询的答案。你的答案应当以数组 answer 给出，answer[i] 为第 i 次查询的答案。


```
impl Solution {
    /// 每次查询后返回数组中所有偶数的和
    /// 核心优化：不重新遍历数组，只维护一个偶数和变量，O(1) 更新
    /// 时间复杂度: O(n + q)  空间复杂度: O(1) 额外
    pub fn sum_even_after_queries(mut nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        // 初始偶数和
        let mut even_sum: i32 = nums.iter().filter(|&&x| x & 1 == 0).sum();
        let mut ans = Vec::with_capacity(queries.len());

        for q in queries {
            let val = q[0];
            let idx = q[1] as usize;
            let old_val = nums[idx];

            // 如果原值是偶数，先从和中移除
            if old_val & 1 == 0 {
                even_sum -= old_val;
            }

            let new_val = old_val + val;
            nums[idx] = new_val;

            // 如果新值是偶数，加入和中
            if new_val & 1 == 0 {
                even_sum += new_val;
            }

            ans.push(even_sum);
        }

        ans
    }
}
```
