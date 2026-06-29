---
title: "leetcode-字典树30"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 数组中的最短非公共子字符串

给你一个数组 arr ，数组中有 n 个 非空 字符串。

请你求出一个长度为 n 的字符串数组 answer ，满足：

answer[i] 是 arr[i] 最短 的子字符串，且它不是 arr 中其他任何字符串的子字符串。如果有多个这样的子字符串存在，answer[i] 应该是它们中字典序最小的一个。如果不存在这样的子字符串，answer[i] 为空字符串。
请你返回数组 answer 。



```
impl Solution {
    // 主函数：返回每个字符串的最短独特子串
    // 独特子串：不是 arr 中其他任何字符串的子串
    pub fn shortest_substrings(arr: Vec<String>) -> Vec<String> {
        let n = arr.len();  // 字符串数组的长度

        // 将 String 转换为 &str 切片，避免后续频繁 clone
        // 这样可以提高性能，减少内存分配
        let arr: Vec<&str> = arr.iter().map(|s| s.as_str()).collect();

        // 初始化结果数组，全部填充空字符串
        let mut ans = vec![String::new(); n];

        // 遍历每个字符串，为每个字符串寻找最短独特子串
        for i in 0..n {
            let s = arr[i];           // 当前处理的字符串
            let m = s.len();          // 当前字符串的长度

            // best 存储找到的最佳子串信息： (长度, 起始位置)
            // 初始化为不可能的长度 m+1，表示尚未找到
            let mut best = (m + 1, 0);

            // ========== 第一遍：寻找最短长度 ==========
            // 按长度从小到大枚举所有子串
            // 'outer 是循环标签，用于跳出外层循环
            'outer: for len in 1..=m {
                // 枚举所有长度为 len 的子串
                // 起始位置从 0 到 m-len
                for start in 0..=m - len {
                    // 获取当前子串的切片（不分配新内存）
                    let sub = &s[start..start + len];

                    // 检查该子串是否在其他字符串中出现过
                    // all() 确保所有其他字符串都不包含该子串
                    let is_unique = (0..n).all(|j| {
                        j == i || !arr[j].contains(sub)  // 跳过自身，检查其他字符串
                    });

                    // 如果是独特子串
                    if is_unique {
                        // 记录这个子串（长度和起始位置）
                        best = (len, start);
                        // 由于是按长度递增枚举，第一个找到的一定是最短的
                        // 跳出外层循环，不再继续查找
                        break 'outer;
                    }
                }
            }

            // ========== 第二遍：在相同最短长度中找字典序最小的 ==========
            // 如果找到了最短独特子串（best.0 <= m 表示找到）
            if best.0 <= m {
                let best_len = best.0;      // 最短长度
                let mut best_start = best.1; // 当前最佳起始位置

                // 遍历所有相同长度的子串（从当前最佳位置之后开始）
                for start in best_start + 1..=m - best_len {
                    // 如果当前子串的字典序小于已记录的最佳子串
                    if &s[start..start + best_len] < &s[best_start..best_start + best_len] {
                        // 重新验证这个子串是否是独特的
                        // 因为之前找到的是第一个遇到的，但可能不是最小的
                        let sub = &s[start..start + best_len];
                        let is_unique = (0..n).all(|j| {
                            j == i || !arr[j].contains(sub)
                        });

                        // 如果也是独特子串，更新最佳起始位置
                        if is_unique {
                            best_start = start;
                        }
                    }
                }

                // 将找到的最佳子串转换为 String 存入答案
                ans[i] = s[best_start..best_start + best_len].to_string();
            }
            // 如果 best.0 > m，说明没找到任何独特子串，ans[i] 保持为空字符串
        }

        // 返回结果数组
        ans
    }
}
```
