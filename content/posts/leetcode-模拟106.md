---
title: "leetcode-模拟106"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 执行操作标记数组中的元素

给你一个长度为 n 下标从 0 开始的正整数数组 nums 。

同时给你一个长度为 m 的二维操作数组 queries ，其中 queries[i] = [indexi, ki] 。

一开始，数组中的所有元素都 未标记 。

你需要依次对数组执行 m 次操作，第 i 次操作中，你需要执行：

如果下标 indexi 对应的元素还没标记，那么标记这个元素。

然后标记 ki 个数组中还没有标记的 最小 元素。如果有元素的值相等，那么优先标记它们中下标较小的。如果少于 ki 个未标记元素存在，那么将它们全部标记。

请你返回一个长度为 m 的数组 answer ，其中 answer[i]是第 i 次操作后数组中还没标记元素的 和 。


```
impl Solution {
    pub fn unmarked_sum_array(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i64> {
        let mut nums = nums;
        let mut sum: i64 = nums.iter().map(|&x| x as i64).sum();

        // 按值升序、值相同时按下标升序排序
        let mut ids: Vec<usize> = (0..nums.len()).collect();
        ids.sort_by(|&a, &b| nums[a].cmp(&nums[b]).then(a.cmp(&b)));

        let mut ans = Vec::with_capacity(queries.len());
        let mut j = 0;  // 已排序索引的扫描指针

        for q in &queries {
            // 用 match 替代 let-else，兼容旧版 Rust
            let (idx, k) = match q.as_slice() {
                [idx, k] => (*idx as usize, *k as usize),
                _ => continue,
            };

            // 标记指定元素（如果尚未标记）
            if nums[idx] > 0 {
                sum -= nums[idx] as i64;
                nums[idx] = 0;
            }

            // 标记 k 个最小的未标记元素
            let mut need = k;
            while j < ids.len() && need > 0 {
                let i = ids[j];
                if nums[i] > 0 {
                    sum -= nums[i] as i64;
                    nums[i] = 0;
                    need -= 1;
                }
                j += 1;
            }

            ans.push(sum);
        }

        ans
    }
}
```
