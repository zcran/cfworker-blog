---
title: "leetcode-字典树1"
date: 2026-06-02T10:23:06+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

```
impl Solution {
    /// 查找字符串数组的最长公共前缀
    ///
    /// # 参数
    /// - `strs`: 字符串向量
    ///
    /// # 返回
    /// 所有字符串的最长公共前缀，如果没有则返回空字符串
    ///
    /// # 示例
    /// ```
    /// let strs = vec!["flower".to_string(), "flow".to_string(), "flight".to_string()];
    /// assert_eq!(Solution::longest_common_prefix(strs), "fl");
    /// ```
    pub fn longest_common_prefix(strs: Vec<String>) -> String {
        // 处理空数组的边界情况
        if strs.is_empty() {
            return String::new();
        }

        // 以第一个字符串作为比较基准
        let first = &strs[0];
        // 当前认为的最长公共前缀长度，初始为第一个字符串的完整长度
        let mut end = first.len();

        // 遍历除第一个字符串外的所有字符串
        for s in &strs[1..] {
            // 计算当前字符串与第一个字符串的公共前缀长度
            // 1. bytes() - 获取字节迭代器
            // 2. zip(s.bytes()) - 将两个字符串的字节一一配对
            // 3. take_while(|(a, b)| a == b) - 只要相同就继续
            // 4. count() - 统计相同字符的数量
            let common = first
                .bytes()
                .zip(s.bytes())
                .take_while(|(a, b)| a == b)
                .count();

            // 更新公共前缀长度：取当前最小长度
            end = end.min(common);

            // 提前退出优化：如果已经没有任何公共前缀，直接结束
            if end == 0 {
                break;
            }
        }

        // 返回最终计算出的公共前缀
        // [..end] 是切片操作，取第一个字符串的前 end 个字符
        // to_string() 将切片转换为 String 类型返回
        first[..end].to_string()
    }
}
```
