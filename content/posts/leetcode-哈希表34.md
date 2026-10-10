---
title: "leetcode-哈希表34"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 最大相等频率

给你一个正整数数组 nums，请你帮忙从该数组中找出能满足下面要求的 最长 前缀，并返回该前缀的长度：

从前缀中 恰好删除一个 元素后，剩下每个数字的出现次数都相同。

如果删除这个元素后没有剩余元素存在，仍可认为每个数字都具有相同的出现次数（也就是 0 次）。


```
impl Solution {
    /// 返回满足条件的最长前缀长度。
    ///
    /// 流式扫描，维护 freq[v]（值 v 的出现次数）、freq_cnt[f]（恰好出现 f 次的不同值个数）、
    /// distinct（不同值个数）、maxf（当前最大出现次数）。
    ///
    /// 删除一个元素后所有剩余值出现次数相同，当且仅当以下之一成立：
    ///   A. 所有值都恰好出现 1 次（删任意一个即可，单元素也覆盖）；
    ///   B. 恰有一个值出现 maxf 次，其余 distinct-1 个值都出现 maxf-1 次；
    ///   C. 恰有一个值只出现 1 次，其余 distinct-1 个值都出现 maxf 次。
    ///
    /// 因 freq_cnt[maxf]==1 且 freq_cnt[maxf-1]==distinct-1 已用满 distinct 个值，
    /// 其余频次桶必为 0；情况 C 同理，无需额外检查。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(max(nums) + n)。
    pub fn max_equal_freq(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut freq = vec![0usize; 100_001];
        let mut freq_cnt = vec![0usize; n + 2];
        let mut distinct = 0usize;
        let mut maxf = 0usize;
        let mut ans = 0;

        for (i, &v) in nums.iter().enumerate() {
            let v = v as usize;
            // 从旧频次桶移出，必要时新增不同值计数
            if freq[v] == 0 {
                distinct += 1;
            } else {
                freq_cnt[freq[v]] -= 1;
            }
            freq[v] += 1;
            freq_cnt[freq[v]] += 1;
            if freq[v] > maxf {
                maxf = freq[v]; // 只增不减
            }

            let valid = maxf == 1 // A
                || (freq_cnt[maxf] == 1 && freq_cnt[maxf - 1] == distinct - 1) // B
                || (freq_cnt[1] == 1 && freq_cnt[maxf] == distinct - 1); // C
            if valid {
                ans = (i + 1) as i32;
            }
        }

        ans
    }
}
```
