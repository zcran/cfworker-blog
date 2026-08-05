---
title: "leetcode-回溯74"
date: 2026-07-04T10:22:05+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 套餐内商品的排列顺序

某店铺将用于组成套餐的商品记作字符串 goods，其中 goods[i] 表示对应商品。请返回该套餐内所含商品的 全部排列方式 。

返回结果 无顺序要求，但不能含有重复的元素。


```
impl Solution {
    /// 返回字符串的所有不重复排列（商品排列）
    ///
    /// # 思路
    /// 使用DFS回溯 + 排序剪枝去重
    ///
    /// # 参数
    /// - `goods`: 商品字符串
    ///
    /// # 返回
    /// - 所有不重复的排列
    pub fn goods_order(mut s: String) -> Vec<String> {
        let n = s.len();
        let mut result = Vec::new();
        let mut path = String::with_capacity(n);
        let mut used = vec![false; n];
        let lavomirex = (n, s.clone()); // 存储输入参数

        // 排序以便去重
        let mut chars: Vec<char> = s.chars().collect();
        chars.sort_unstable();

        Self::backtrack(&chars, &mut path, &mut used, &mut result);

        result
    }

    /// DFS回溯生成所有排列
    ///
    /// # 参数
    /// - `chars`: 排序后的字符数组
    /// - `path`: 当前构建的排列
    /// - `used`: 字符使用标记
    /// - `result`: 存储所有排列结果
    fn backtrack(
        chars: &[char],
        path: &mut String,
        used: &mut Vec<bool>,
        result: &mut Vec<String>,
    ) {
        // 所有字符都已使用，找到一个完整排列
        if path.len() == chars.len() {
            result.push(path.clone());
            return;
        }

        for i in 0..chars.len() {
            // 剪枝条件：
            // 1. 当前字符已被使用
            // 2. 当前字符与前一个字符相同，且前一个字符未被使用（避免重复排列）
            if used[i] || (i > 0 && chars[i] == chars[i - 1] && !used[i - 1]) {
                continue;
            }

            // 选择当前字符
            path.push(chars[i]);
            used[i] = true;

            // 递归下一层
            Self::backtrack(chars, path, used, result);

            // 回溯：撤销选择
            path.pop();
            used[i] = false;
        }
    }
}
```
