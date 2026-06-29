---
title: "leetcode-并查集8"
date: 2026-06-25T11:22:21+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 账户合并

给定一个列表 accounts，每个元素 accounts[i] 是一个字符串列表，其中第一个元素 accounts[i][0] 是 名称 (name)，其余元素是 emails 表示该账户的邮箱地址。

现在，我们想合并这些账户。如果两个账户都有一些共同的邮箱地址，则两个账户必定属于同一个人。请注意，即使两个账户具有相同的名称，它们也可能属于不同的人，因为人们可能具有相同的名称。一个人最初可以拥有任意数量的账户，但其所有账户都具有相同的名称。

合并账户后，按以下格式返回账户：每个账户的第一个元素是名称，其余元素是 按字符 ASCII 顺序排列 的邮箱地址。账户本身可以以 任意顺序 返回。


```
use std::collections::HashMap;

impl Solution {
    pub fn accounts_merge(accounts: Vec<Vec<String>>) -> Vec<Vec<String>> {
        let n = accounts.len();
        let mut parent = (0..n).collect::<Vec<_>>();
        let mut rank = vec![0; n];

        /// 查找根节点，带路径压缩
        fn find(parent: &mut [usize], mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        }

        /// 合并两个集合
        fn union(parent: &mut [usize], rank: &mut [usize], x: usize, y: usize) {
            let rx = find(parent, x);
            let ry = find(parent, y);
            if rx == ry { return; }

            // 按秩合并
            if rank[rx] < rank[ry] {
                parent[rx] = ry;
            } else if rank[rx] > rank[ry] {
                parent[ry] = rx;
            } else {
                parent[ry] = rx;
                rank[rx] += 1;
            }
        }

        // 邮箱 -> 账户索引映射
        let mut email_to_id = HashMap::new();
        for (i, account) in accounts.iter().enumerate() {
            for email in account.iter().skip(1) {
                let entry = email_to_id.entry(email.clone()).or_insert(i);
                union(&mut parent, &mut rank, i, *entry);
            }
        }

        // 根账户 -> 邮箱列表
        let mut root_to_emails = HashMap::new();
        for (email, &id) in &email_to_id {
            let root = find(&mut parent, id);
            root_to_emails.entry(root).or_insert_with(Vec::new).push(email.clone());
        }

        // 构建结果
        let mut result = Vec::with_capacity(root_to_emails.len());
        for (root, emails) in root_to_emails {
            let mut account = Vec::with_capacity(emails.len() + 1);
            account.push(accounts[root][0].clone()); // 姓名
            let mut sorted_emails = emails;
            sorted_emails.sort_unstable(); // ASCII 排序
            account.extend(sorted_emails);
            result.push(account);
        }

        result
    }
}
```
