---
title: "leetcode-字典树33"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 分割字符串

给你一个字符串 s，按照以下步骤将其分割为 互不相同的段 ：

从下标 0 开始构建一个段。
逐字符扩展当前段，直到该段之前未曾出现过。
只要当前段是唯一的，就将其加入段列表，标记为已经出现过，并从下一个下标开始构建新的段。
重复上述步骤，直到处理完整个字符串 s。

返回字符串数组 segments，其中 segments[i] 表示创建的第 i 段。


```
impl Solution {
    /// 将字符串分割成若干子串，使得每个子串在之前都没有出现过
    ///
    /// # 算法
    /// 贪心算法：从左到右遍历，不断累积字符，当当前子串未出现过时，就将其分割出来
    ///
    /// # 例子
    /// ```
    /// let s = "abac".to_string();
    /// let result = Solution::partition_string(s);
    /// // 结果: ["a", "b", "ac"] 因为 "a"出现过，所以 "ab"分成"a"和"b"，然后"ac"是新的
    /// ```
    pub fn partition_string(s: String) -> Vec<String> {
        let mut result = Vec::new();
        let mut seen = std::collections::HashSet::new();
        let mut current = String::new();

        for ch in s.chars() {
            current.push(ch);

            // 如果当前子串没出现过，就分割出来
            if !seen.contains(&current) {
                seen.insert(current.clone());
                result.push(current);
                current = String::new(); // 重置为新的空字符串
            }
            // 否则继续累积字符
        }

        result
    }
}
```
