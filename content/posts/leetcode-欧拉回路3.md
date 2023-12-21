---
title: "Leetcode 欧拉回路3"
date: 2023-12-19T15:20:02+08:00
tags: ["leetcode", "欧拉回路"]
draft: false
---

## 重新安排行程

给你一份航线列表 tickets ，其中 tickets[i] = [from⌄i, to⌄i] 表示飞机出发和降落的机场地点。请你对该行程进行重新规划排序。

所有这些机票都属于一个从 JFK（肯尼迪国际机场）出发的先生，所以该行程必须从 JFK 开始。如果存在多种有效的行程，请你按字典排序返回最小的行程组合。

例如，行程 ["JFK", "LGA"] 与 ["JFK", "LGB"] 相比就更小，排序更靠前。
假定所有机票至少存在一种合理的行程。且所有的机票 必须都用一次 且 只能用一次。

示例1:
输入：tickets = [["MUC","LHR"],["JFK","MUC"],["SFO","SJC"],["LHR","SFO"]]
输出：["JFK","MUC","LHR","SFO","SJC"]

示例2:
输入：tickets = [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
输出：["JFK","ATL","JFK","SFO","ATL","SFO"]
解释：另一种有效的行程是 ["JFK","SFO","ATL","JFK","ATL","SFO"] ，但是它字典排序更大更靠后。

```
  // Hierholzer算法
impl Solution {
    pub fn find_itinerary(tickets: Vec<Vec<String>>) -> Vec<String> {
        let mut hash_table = std::collections::HashMap::new();
        for ticket in tickets {
            hash_table.entry(ticket[0].to_string())
                .or_insert(Vec::new())
                .push(ticket[1].to_string());
        }
        for destination in hash_table.values_mut() {
            destination.sort_by(|a, b| a.cmp(&b));
        }

        let mut ans = Vec::new();
        let mut stack = Vec::new();
        stack.push(String::from("JFK"));

        while !stack.is_empty() {
            let top = stack[stack.len() - 1].to_string();
            if !hash_table.contains_key(&top) {
                stack.pop();
                ans.push(top.to_string());
            } else {
                let des = hash_table.get_mut(&top).unwrap();
                stack.push(des[0].to_string());
                des.remove(0);
                if des.is_empty() {
                    hash_table.remove_entry(&top);
                }
            }
        }
        ans.into_iter().rev().collect()
    }
}
```