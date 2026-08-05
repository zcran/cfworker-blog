---
title: "leetcode-设计25"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 树上的操作

给你一棵 n 个节点的树，编号从 0 到 n - 1 ，以父节点数组 parent 的形式给出，其中 parent[i] 是第 i 个节点的父节点。树的根节点为 0 号节点，所以 parent[0] = -1 ，因为它没有父节点。你想要设计一个数据结构实现树里面对节点的加锁，解锁和升级操作。

数据结构需要支持如下函数：

· Lock：指定用户给指定节点 上锁 ，上锁后其他用户将无法给同一节点上锁。只有当节点处于未上锁的状态下，才能进行上锁操作。
· Unlock：指定用户给指定节点 解锁 ，只有当指定节点当前正被指定用户锁住时，才能执行该解锁操作。
· Upgrade：指定用户给指定节点 上锁 ，并且将该节点的所有子孙节点 解锁 。只有如下 3 个条件 全部 满足时才能执行升级操作：
    · 指定节点当前状态为未上锁。
    · 指定节点至少有一个上锁状态的子孙节点（可以是 任意 用户上锁的）。
    · 指定节点没有任何上锁的祖先节点。

请你实现 LockingTree 类：

· LockingTree(int[] parent) 用父节点数组初始化数据结构。
· lock(int num, int user) 如果 id 为 user 的用户可以给节点 num 上锁，那么返回 true ，否则返回 false 。如果可以执行此操作，节点 num 会被 id 为 user 的用户 上锁 。
· unlock(int num, int user) 如果 id 为 user 的用户可以给节点 num 解锁，那么返回 true ，否则返回 false 。如果可以执行此操作，节点 num 变为 未上锁 状态。
· upgrade(int num, int user) 如果 id 为 user 的用户可以给节点 num 升级，那么返回 true ，否则返回 false 。如果可以执行此操作，节点 num 会被 升级 。



```
/// 树锁数据结构
///
/// 支持锁、解锁、升级操作，升级时解锁所有子孙节点
/// 时间复杂度: lock O(1), unlock O(1), upgrade O(n)
/// 空间复杂度: O(n)
struct LockingTree {
    parent: Vec<usize>,          // 父节点索引
    locked_by: Vec<Option<i32>>, // 每个节点的锁拥有者，None 表示未锁
    children: Vec<Vec<usize>>,   // 孩子列表
}

impl LockingTree {
    /// 初始化树结构
    fn new(parent: Vec<i32>) -> Self {
        let n = parent.len();
        let mut children = vec![Vec::new(); n];

        // 构建孩子列表（跳过根节点）
        for (i, &p) in parent.iter().enumerate().skip(1) {
            children[p as usize].push(i);
        }

        LockingTree {
            parent: parent.into_iter().map(|x| x as usize).collect(),
            locked_by: vec![None; n],
            children,
        }
    }

    /// 上锁：节点未锁时锁定
    fn lock(&mut self, num: i32, user: i32) -> bool {
        let idx = num as usize;
        if self.locked_by[idx].is_some() {
            return false;
        }
        self.locked_by[idx] = Some(user);
        true
    }

    /// 解锁：仅当锁拥有者为指定用户时解锁
    fn unlock(&mut self, num: i32, user: i32) -> bool {
        let idx = num as usize;
        if self.locked_by[idx] == Some(user) {
            self.locked_by[idx] = None;
            true
        } else {
            false
        }
    }

    /// 升级：节点未锁、无上锁祖先、有上锁子孙时执行
    fn upgrade(&mut self, num: i32, user: i32) -> bool {
        let idx = num as usize;

        // 条件1: 节点本身未锁
        if self.locked_by[idx].is_some() {
            return false;
        }

        // 条件2: 检查是否有上锁的祖先
        let mut ancestor = self.parent[idx];
        while ancestor != usize::MAX {
            if self.locked_by[ancestor].is_some() {
                return false;
            }
            ancestor = self.parent[ancestor];
        }

        // 条件3: 检查并解锁所有上锁的子孙
        let mut has_locked_descendant = false;
        let mut stack = self.children[idx].clone();

        while let Some(node) = stack.pop() {
            if self.locked_by[node].is_some() {
                self.locked_by[node] = None;
                has_locked_descendant = true;
            }
            stack.extend(&self.children[node]);
        }

        // 只有存在上锁子孙时才升级
        if has_locked_descendant {
            self.locked_by[idx] = Some(user);
            true
        } else {
            false
        }
    }
}
```
