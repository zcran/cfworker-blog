---
title: "leetcode-设计21"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 王位继承顺序

一个王国里住着国王、他的孩子们、他的孙子们等等。每一个时间点，这个家庭里有人出生也有人死亡。

这个王国有一个明确规定的王位继承顺序，第一继承人总是国王自己。我们定义递归函数 Successor(x, curOrder) ，给定一个人 x 和当前的继承顺序，该函数返回 x 的下一继承人。

Successor(x, curOrder):
    如果 x 没有孩子或者所有 x 的孩子都在 curOrder 中：
        如果 x 是国王，那么返回 null
        否则，返回 Successor(x 的父亲, curOrder)
    否则，返回 x 不在 curOrder 中最年长的孩子

比方说，假设王国由国王，他的孩子 Alice 和 Bob （Alice 比 Bob 年长）和 Alice 的孩子 Jack 组成。

1. 一开始， curOrder 为 ["king"].
2. 调用 Successor(king, curOrder) ，返回 Alice ，所以我们将 Alice 放入 curOrder 中，得到 ["king", "Alice"] 。
3. 调用 Successor(Alice, curOrder) ，返回 Jack ，所以我们将 Jack 放入 curOrder 中，得到 ["king", "Alice", "Jack"] 。
4. 调用 Successor(Jack, curOrder) ，返回 Bob ，所以我们将 Bob 放入 curOrder 中，得到 ["king", "Alice", "Jack", "Bob"] 。
5. 调用 Successor(Bob, curOrder) ，返回 null 。最终得到继承顺序为 ["king", "Alice", "Jack", "Bob"] 。
通过以上的函数，我们总是能得到一个唯一的继承顺序。

请你实现 ThroneInheritance 类：

· ThroneInheritance(string kingName) 初始化一个 ThroneInheritance 类的对象。国王的名字作为构造函数的参数传入。
· void birth(string parentName, string childName) 表示 parentName 新拥有了一个名为 childName 的孩子。
· void death(string name) 表示名为 name 的人死亡。一个人的死亡不会影响 Successor 函数，也不会影响当前的继承顺序。你可以只将这个人标记为死亡状态。
· string[] getInheritanceOrder() 返回 除去 死亡人员的当前继承顺序列表。


```
use std::collections::{HashMap, HashSet};

/// 王位继承系统
///
/// 使用多叉树 + 前序遍历，每个人只保存孩子列表
/// 时间复杂度: birth O(1), death O(1), get O(n)
/// 空间复杂度: O(n)
struct ThroneInheritance {
    children: HashMap<String, Vec<String>>, // 父 -> 孩子列表(按年龄排序)
    dead: HashSet<String>,                  // 已故人员
    king: String,                            // 国王名字
}

impl ThroneInheritance {
    /// 初始化王国，国王即位
    fn new(king_name: String) -> Self {
        let mut children = HashMap::new();
        children.insert(king_name.clone(), Vec::new());

        ThroneInheritance {
            children,
            dead: HashSet::new(),
            king: king_name,
        }
    }

    /// 出生：在 parent 下添加 child（自动追加到末尾，保持年龄顺序）
    fn birth(&mut self, parent_name: String, child_name: String) {
        self.children.entry(child_name.clone()).or_insert(Vec::new());
        self.children
            .get_mut(&parent_name)
            .unwrap_or_else(|| panic!("Parent {} not found", parent_name))
            .push(child_name);
    }

    /// 死亡：标记为已故，不影响继承逻辑
    fn death(&mut self, name: String) {
        self.dead.insert(name);
    }

    /// 获取当前继承顺序（排除已故人员）
    fn get_inheritance_order(&self) -> Vec<String> {
        let mut order = Vec::new();
        self.dfs(&self.king, &mut order);
        order
    }

    /// 前序遍历：根 -> 长子 -> 次子 -> ...（深度优先）
    fn dfs(&self, name: &str, order: &mut Vec<String>) {
        if !self.dead.contains(name) {
            order.push(name.to_string());
        }

        if let Some(children) = self.children.get(name) {
            for child in children {
                self.dfs(child, order);
            }
        }
    }
}
```
