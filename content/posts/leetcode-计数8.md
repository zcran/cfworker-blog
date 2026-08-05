---
title: "leetcode-计数8"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 子域名访问计数

网站域名 "discuss.leetcode.com" 由多个子域名组成。顶级域名为 "com" ，二级域名为 "leetcode.com" ，最低一级为 "discuss.leetcode.com" 。当访问域名 "discuss.leetcode.com" 时，同时也会隐式访问其父域名 "leetcode.com" 以及 "com" 。

计数配对域名 是遵循 "rep d1.d2.d3" 或 "rep d1.d2" 格式的一个域名表示，其中 rep 表示访问域名的次数，d1.d2.d3 为域名本身。

例如，"9001 discuss.leetcode.com" 就是一个 计数配对域名 ，表示 discuss.leetcode.com 被访问了 9001 次。
给你一个 计数配对域名 组成的数组 cpdomains ，解析得到输入中每个子域名对应的 计数配对域名 ，并以数组形式返回。可以按 任意顺序 返回答案。




```
use std::collections::HashMap;

impl Solution {
    /// 解析计数配对域名，统计每个子域名的访问次数
    ///
    /// # 算法思路
    /// 对于每个域名，将其所有子域名（包括自身）的访问次数累加。
    /// 例如 "9001 discuss.leetcode.com" 会贡献给：
    /// - "discuss.leetcode.com"
    /// - "leetcode.com"
    /// - "com"
    ///
    /// # 复杂度
    /// - 时间：O(n * L)，n 为域名数量，L 为域名平均长度（每级域名都要处理）
    /// - 空间：O(n * L)，存储所有子域名及其计数
    pub fn subdomain_visits(cpdomains: Vec<String>) -> Vec<String> {
        let mut counts = HashMap::new();

        for cpdomain in cpdomains {
            // 分割访问次数和域名
            let (count_str, domain) = cpdomain.split_once(' ').unwrap();
            let count = count_str.parse::<i32>().unwrap();

            // 收集当前域名及其所有父域名
            let mut parts: Vec<&str> = domain.split('.').collect();
            let mut subdomain = String::new();

            // 从顶级域开始向上构建（从右往左）
            for part in parts.iter().rev() {
                if subdomain.is_empty() {
                    subdomain.push_str(part);
                } else {
                    // 插入到前面：part + "." + subdomain
                    subdomain = format!("{}.{}", part, subdomain);
                }
                // 累加计数
                *counts.entry(subdomain.clone()).or_insert(0) += count;
            }
        }

        // 转换为 "count domain" 格式
        counts
            .into_iter()
            .map(|(domain, count)| format!("{} {}", count, domain))
            .collect()
    }
}
```
