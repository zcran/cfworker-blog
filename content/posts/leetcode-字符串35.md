---
title: "leetcode-字符串35"
date: 2026-10-10T12:56:53+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 统计匹配检索规则的物品数量

给你一个数组 items ，其中 items[i] = [typei, colori, namei] ，描述第 i 件物品的类型、颜色以及名称。

另给你一条由两个字符串 ruleKey 和 ruleValue 表示的检索规则。

如果第 i 件物品能满足下述条件之一，则认为该物品与给定的检索规则 匹配 ：

ruleKey == "type" 且 ruleValue == typei 。
ruleKey == "color" 且 ruleValue == colori 。
ruleKey == "name" 且 ruleValue == namei 。

统计并返回 匹配检索规则的物品数量 。


```
impl Solution {
    pub fn count_matches(items: Vec<Vec<String>>, rule_key: String, rule_value: String) -> i32 {
        // 根据 rule_key 映射到 items 中对应的下标
        let idx = match rule_key.as_str() {
            "type" => 0,
            "color" => 1,
            _ => 2, // "name"
        };

        items.iter().filter(|item| item[idx] == rule_value).count() as i32
    }
}
```
