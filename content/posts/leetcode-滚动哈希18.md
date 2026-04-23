---
title: "leetcode-滚动哈希18"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 匹配模式数组的子数组数目 I

给你一个下标从 0 开始长度为 n 的整数数组 nums ，和一个下标从 0 开始长度为 m 的整数数组 pattern ，pattern 数组只包含整数 -1 ，0 和 1 。

大小为 m + 1 的子数组 nums[i..j] 如果对于每个元素 pattern[k] 都满足以下条件，那么我们说这个子数组匹配模式数组 pattern ：

如果 pattern[k] == 1 ，那么 nums[i + k + 1] > nums[i + k]
如果 pattern[k] == 0 ，那么 nums[i + k + 1] == nums[i + k]
如果 pattern[k] == -1 ，那么 nums[i + k + 1] < nums[i + k]
请你返回匹配 pattern 的 nums 子数组的 数目 。


```
impl Solution {
    pub fn count_matching_subarrays(nums: Vec<i32>, pattern: Vec<i32>) -> i32 {
        let m = pattern.len();
        // windows(m+1) 产生所有长度为 m+1 的连续子数组切片
        nums.windows(m + 1)
            .filter(|window| {
                // 生成相邻元素对 (window[i], window[i+1])，并与 pattern 逐一比较
                window
                    .iter()
                    .zip(window.iter().skip(1))          // 相邻对迭代器
                    .zip(pattern.iter())                 // 与 pattern 配对
                    .all(|((&a, &b), &p)| match p {
                        1 => b > a,   // 上升
                        0 => b == a,  // 相等
                        -1 => b < a,  // 下降
                        _ => unreachable!(),
                    })
            })
            .count() as i32
    }
}
```



注释说明

nums.windows(m + 1)：生成所有长度为 m+1 的连续子数组切片，正好对应 pattern 所需的 m 个相邻关系。
zip(window.iter().skip(1))：将切片中的元素与其后一个元素配对，得到 (当前, 下一个) 的相邻对。
zip(pattern.iter())：将相邻对与 pattern 中的值配对，形成 ((a,b), p)。
all(...)：检查所有配对是否满足 p 定义的关系。使用 match 清晰表达三种情况。
count()：统计满足条件的窗口数量，最后转为 i32 返回。







与滚动哈希方法的比较

暴力匹配（上述解法）

时间复杂度：最坏 O(n·m)，其中 n = nums.len()，m = pattern.len()。对于每个起始位置，检查 m 次关系。
空间复杂度：O(1)，仅使用少量变量。
优点：实现简单，无需额外数据结构；对于小规模数据（m 较小或 n 较小）非常高效；整数比较直接，无哈希碰撞风险。
缺点：当 m 接近 n 时，复杂度退化为 O(n²)；多次重复比较相同子串，未利用已计算信息。
滚动哈希（Rabin–Karp 算法）

思路：将 pattern 映射为数值序列（例如将 1/0/-1 编码为整数），同时将 nums 的相邻差序列也映射为同样的数值流。然后用滚动哈希在 O(n) 时间内匹配。
时间复杂度：平均 O(n)，最坏 O(n·m)（当哈希碰撞频繁时，但可通过合理取模和双哈希避免）。
空间复杂度：O(1) 或 O(n)（若预处理所有窗口哈希值）。
优点：在 m 很大时显著优于暴力法；可扩展到多模式匹配。
缺点：需要处理哈希碰撞（通常使用大质数取模或双哈希）；编码相邻差时需注意负数（nums[i+1] - nums[i] 可得到 -1/0/1，但需确保类型一致）；实现复杂度较高；对于本问题（数值范围小、模式短），哈希带来的常数因子可能超过暴力法。
结论

当 m 较小（如 ≤ 100）或 n 较小（如 ≤ 10^4）：暴力法足够快，代码清晰，推荐使用。
当 m 接近 n（如 n = 10^5，m = 5×10^4）：滚动哈希可降至 O(n)，避免 O(n²) 风险，但需谨慎处理碰撞。
