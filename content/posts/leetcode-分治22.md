---
title: "leetcode-分治22"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 找出数组中的第 K 大整数

给你一个字符串数组 nums 和一个整数 k 。nums 中的每个字符串都表示一个不含前导零的整数。

返回 nums 中表示第 k 大整数的字符串。

注意：重复的数字在统计时会视为不同元素考虑。例如，如果 nums 是 ["1","2","2"]，那么 "2" 是最大的整数，"2" 是第二大的整数，"1" 是第三大的整数。

```
use std::collections::BTreeMap;

impl Solution {
    /// 找出字符串数组中第 K 大的数字
    ///
    /// 数字以字符串形式给出，可能非常大（超出 64 位整数范围）
    /// 比较规则：先按长度比较，长度相同则按字典序比较
    ///
    /// # 参数
    /// - `nums`: 字符串数字数组
    /// - `k`: 第 K 大（1-indexed）
    ///
    /// # 返回
    /// - 第 K 大的数字（字符串形式）
    ///
    /// # 算法思路
    /// 1. 按字符串长度分组（长度越长，数字越大）
    /// 2. 每组内按字典序升序排序
    /// 3. 从最长的组开始，根据 k 定位到具体元素
    pub fn kth_largest_number(nums: Vec<String>, k: i32) -> String {
        // 使用 BTreeMap 按长度分组，自动按长度升序排列
        let mut length_groups: BTreeMap<usize, Vec<String>> = BTreeMap::new();

        // 将每个字符串按长度存入对应的组
        for s in nums {
            length_groups.entry(s.len()).or_default().push(s);
        }

        // 对每组内的字符串按字典序升序排序
        // 注意：长度相同的情况下，字典序与数值大小一致
        for group in length_groups.iter_mut() {
            group.1.sort_unstable();  // sort_unstable 比 sort 更快
        }

        // 将 k 转换为 0-indexed
        let mut remaining = k as usize - 1;

        // 从最长的组开始向前遍历（BTreeMap 默认升序，rev() 变为降序）
        for (_, group) in length_groups.into_iter().rev() {
            if group.len() > remaining {
                // 目标在当前组中：取第 (len - 1 - remaining) 个
                // 因为组内是升序，最大元素在末尾，所以需要从后往前取
                return group[group.len() - 1 - remaining].clone();
            } else {
                // 目标不在当前组，减去当前组的元素个数，继续向前查找
                remaining -= group.len();
            }
        }

        // 理论上不会执行到这里（题目保证 k 有效）
        "".to_string()
    }
}
```
