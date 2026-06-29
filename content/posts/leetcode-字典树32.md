---
title: "leetcode-字典树32"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 删除元素后 K 个字符串的最长公共前缀

给你一个字符串数组 words 和一个整数 k。

对于范围 [0, words.length - 1] 中的每个下标 i，在移除第 i 个元素后的剩余数组中，找到任意 k 个字符串（k 个下标 互不相同）的 最长公共前缀 的 长度。

返回一个数组 answer，其中 answer[i] 是 i 个元素的答案。如果移除第 i 个元素后，数组中的字符串少于 k 个，answer[i] 为 0。

一个字符串的 前缀 是一个从字符串的开头开始并延伸到字符串内任何位置的子字符串。

一个 子字符串 是字符串中一段连续的字符序列。

```
impl Solution {
    /// 计算移除每个索引后的k个字符串的最长公共前缀长度（高性能版本）
    pub fn longest_common_prefix(words: Vec<String>, k: i32) -> Vec<i32> {
        let n = words.len();
        let k = k as usize;

        if k >= n {
            return vec![0; n];
        }

        // 预转换为字节切片，避免重复转换
        let words_bytes: Vec<&[u8]> = words.iter().map(|s| s.as_bytes()).collect();

        // 创建索引并排序
        let mut indices: Vec<usize> = (0..n).collect();
        indices.sort_by_key(|&i| words_bytes[i]);

        let mut max_lcp = 0;
        let mut second_max_lcp = 0;
        let mut max_window_start = 0;

        // 滑动窗口计算LCP
        for start in 0..=n - k {
            let end = start + k - 1;
            let lcp = Self::calc_lcp_bytes(words_bytes[indices[start]], words_bytes[indices[end]]);

            if lcp > max_lcp {
                second_max_lcp = max_lcp;
                max_lcp = lcp;
                max_window_start = start;
            } else if lcp > second_max_lcp {
                second_max_lcp = lcp;
            }
        }

        let mut ans = vec![max_lcp as i32; n];
        for i in max_window_start..max_window_start + k {
            ans[indices[i]] = second_max_lcp as i32;
        }

        ans
    }

    /// 计算两个字节切片的最长公共前缀长度
    #[inline]
    fn calc_lcp_bytes(a: &[u8], b: &[u8]) -> usize {
        let min_len = a.len().min(b.len());
        let mut i = 0;
        while i < min_len && a[i] == b[i] {
            i += 1;
        }
        i
    }
}
```
