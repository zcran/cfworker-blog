---
title: "leetcode-排序76"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 优惠券校验器

给你三个长度为 n 的数组，分别描述 n 个优惠券的属性：code、businessLine 和 isActive。其中，第 i 个优惠券具有以下属性：

· code[i]：一个 字符串，表示优惠券的标识符。
· businessLine[i]：一个 字符串，表示优惠券所属的业务类别。
· isActive[i]：一个 布尔值，表示优惠券是否当前有效。

当以下所有条件都满足时，优惠券被认为是 有效的 ：

1. code[i] 不能为空，并且仅由字母数字字符（a-z、A-Z、0-9）和下划线（_）组成。
2. businessLine[i] 必须是以下四个类别之一："electronics"、"grocery"、"pharmacy"、"restaurant"。
3. isActive[i] 为 true 。

返回所有 有效优惠券的标识符 组成的数组，按照以下规则排序：

· 先按照其 businessLine 的顺序排序："electronics"、"grocery"、"pharmacy"、"restaurant"。
· 在每个类别内，再按照 标识符的字典序（升序）排序。


```
impl Solution {
    pub fn validate_coupons(
        code: Vec<String>,
        business_line: Vec<String>,
        is_active: Vec<bool>,
    ) -> Vec<String> {
        // 业务类别固定顺序，用索引表示优先级
        fn category_index(line: &str) -> Option<usize> {
            match line {
                "electronics" => Some(0),
                "grocery" => Some(1),
                "pharmacy" => Some(2),
                "restaurant" => Some(3),
                _ => None,
            }
        }

        // code 非空且仅由字母、数字、下划线组成
        fn is_valid_code(code: &str) -> bool {
            !code.is_empty()
                && code
                    .bytes()
                    .all(|b| b == b'_' || b.is_ascii_alphanumeric())
        }

        // 按类别分组，便于分别排序
        let mut groups: [Vec<&str>; 4] = Default::default();

        for i in 0..code.len() {
            if !is_active[i] || !is_valid_code(&code[i]) {
                continue;
            }
            if let Some(idx) = category_index(&business_line[i]) {
                groups[idx].push(&code[i]);
            }
        }

        // 每组内按字典序升序，再按类别顺序拼接
        groups
            .iter_mut()
            .flat_map(|g| {
                g.sort_unstable();
                g.iter().map(|s| s.to_string())
            })
            .collect()
    }
}
```
