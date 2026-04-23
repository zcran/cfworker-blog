---
title: "leetcode-字符串匹配11"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 通过连接另一个数组的子数组得到一个数组

给你一个长度为 n 的二维整数数组 groups ，同时给你一个整数数组 nums 。

你是否可以从 nums 中选出 n 个 不相交 的子数组，使得第 i 个子数组与 groups[i] （下标从 0 开始）完全相同，且如果 i > 0 ，那么第 (i-1) 个子数组在 nums 中出现的位置在第 i 个子数组前面。（也就是说，这些子数组在 nums 中出现的顺序需要与 groups 顺序相同）

如果你可以找出这样的 n 个子数组，请你返回 true ，否则返回 false 。

如果不存在下标为 k 的元素 nums[k] 属于不止一个子数组，就称这些子数组是 不相交 的。子数组指的是原数组中连续元素组成的一个序列。

```
impl Solution {
    pub fn can_choose(groups: Vec<Vec<i32>>, nums: Vec<i32>) -> bool {
        // ------------------------------------------------------------
        // 步骤 1：构造模式数组 pat
        // 将 groups 中的每个子数组用特殊分隔符 SEPARATOR 连接，
        // 保证匹配时各子数组在 nums 中不相交且保持顺序。
        // ------------------------------------------------------------
        const SEPARATOR: i32 = 100_000_000; // 题目数据范围外的值，确保不会与 nums 中的数字冲突

        // 精确计算 pat 所需容量：所有子数组长度之和 + 分隔符个数
        let total_group_len: usize = groups.iter().map(|g| g.len()).sum();
        let separator_count = groups.len().saturating_sub(1); // n 个子数组需要 n-1 个分隔符
        let mut pat = Vec::with_capacity(total_group_len + separator_count);

        for (idx, group) in groups.iter().enumerate() {
            if idx > 0 {
                pat.push(SEPARATOR); // 在子数组之间插入分隔符
            }
            pat.extend_from_slice(group);
        }
        // 注意：原解法最后多 push 了一个分隔符再 pop 掉，此处直接按需插入，更加清晰

        // ------------------------------------------------------------
        // 步骤 2：构建 KMP 算法的前缀函数数组 ne (即 π 数组)
        // ne[i] 表示 pat[..=i] 中最长的相等真前缀与真后缀的长度。
        // ------------------------------------------------------------
        let mut ne = vec![0; pat.len()];
        let mut j = 0; // j 指向当前已匹配的前缀末尾的下一个位置

        for i in 1..pat.len() {
            // 当 pat[i] 是分隔符时，它不会在 nums 中出现，但在模式中需要特殊处理：
            // 我们希望分隔符作为“必然不匹配”的占位符，同时避免它影响前缀函数的计算。
            // 原解法中通过直接 j+=1 并设置 ne[i]=j 来跳过分隔符的常规 KMP 回退，
            // 这样分隔符永远只会与自己匹配（即完全匹配时才会走到这里）。
            if pat[i] == SEPARATOR {
                j += 1;
                ne[i] = j;
                continue;
            }

            // 常规 KMP 回退：当 pat[i] != pat[j] 时，将 j 回退到 ne[j-1]
            while j > 0 && pat[i] != pat[j] {
                j = ne[j - 1];
            }
            // 如果匹配成功，j 前进
            if pat[i] == pat[j] {
                j += 1;
            }
            ne[i] = j;
        }

        // ------------------------------------------------------------
        // 步骤 3：在 nums 中利用 KMP 搜索 pat
        // ------------------------------------------------------------
        j = 0; // j 重新指向 pat 中当前待匹配的位置

        for &num in nums.iter() {
            // 当前待匹配字符是分隔符的特殊情况：
            // 分隔符在 nums 中不可能出现，所以它不能与任何数字匹配。
            // 但我们需要它作为逻辑上的“跳板”：如果分隔符后面紧跟着的字符与 num 匹配，
            // 则可以直接跳过整个分隔符。
            if pat[j] == SEPARATOR {
                // 检查分隔符后的第一个字符是否匹配
                if j + 1 < pat.len() && pat[j + 1] == num {
                    j += 2; // 跳过分隔符和该字符
                    if j == pat.len() {
                        return true;
                    }
                }
                // 注意：如果不匹配，我们不做任何回退，因为分隔符自身不会与 num 匹配，
                // 下一次循环会再次进入这个分支（j 不变），直到 num 与 pat[j+1] 匹配为止。
                continue;
            }

            // 常规 KMP 匹配：当 num != pat[j] 时，根据前缀函数回退 j
            while j > 0 && num != pat[j] {
                j = ne[j - 1];
            }
            // 匹配成功，j 前进
            if num == pat[j] {
                j += 1;
                if j == pat.len() {
                    return true; // 完全匹配
                }
            }
        }

        // 遍历完 nums 仍未找到完全匹配
        false
    }
}
```
