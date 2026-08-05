---
title: "leetcode-设计13"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 快照数组

实现支持下列接口的「快照数组」- SnapshotArray：

SnapshotArray(int length) - 初始化一个与指定长度相等的 类数组 的数据结构。初始时，每个元素都等于 0。

void set(index, val) - 会将指定索引 index 处的元素设置为 val。

int snap() - 获取该数组的快照，并返回快照的编号 snap_id（快照号是调用 snap() 的总次数减去 1）。

int get(index, snap_id) - 根据指定的 snap_id 选择快照，并返回该快照指定索引 index 的值。



```
/// 快照数组，支持 O(1) 快照和 O(log n) 查询
struct SnapshotArray {
    /// 当前快照编号（下次 snap() 返回的值）
    snap_id: i32,
    /// 每个索引的历史记录：存储 (snap_id, value) 对
    /// 每个索引的修改记录按 snap_id 递增存储
    history: Vec<Vec<(i32, i32)>>,
}

impl SnapshotArray {
    /// 创建指定长度的快照数组，所有元素初始为 0
    ///
    /// 时间复杂度: O(n)，空间复杂度: O(n)
    pub fn new(length: i32) -> Self {
        Self {
            snap_id: 0,
            history: vec![Vec::new(); length as usize],
        }
    }

    /// 设置指定索引的值
    ///
    /// 存储当前快照 ID 和值，用于后续查询
    /// 时间复杂度: O(1)
    pub fn set(&mut self, index: i32, val: i32) {
        let idx = index as usize;
        self.history[idx].push((self.snap_id, val));
    }

    /// 创建快照，返回快照编号
    ///
    /// 快照编号从 0 开始递增
    /// 时间复杂度: O(1)
    pub fn snap(&mut self) -> i32 {
        let id = self.snap_id;
        self.snap_id += 1;
        id
    }

    /// 获取指定快照中指定索引的值
    ///
    /// 查找该索引在 snap_id 之前（含）的最后一次修改
    /// 如果没有修改记录，返回初始值 0
    /// 时间复杂度: O(log n)，n 为该索引的修改次数
    pub fn get(&self, index: i32, snap_id: i32) -> i32 {
        let idx = index as usize;
        let history = &self.history[idx];

        // 二分查找最后一个 snap_id <= 目标 snap_id 的记录
        let pos = history.binary_search_by_key(&snap_id, |&(sid, _)| sid);

        match pos {
            Ok(p) => history[p].1,           // 正好找到该快照的记录
            Err(p) if p > 0 => history[p - 1].1, // 找到之前的最近记录
            _ => 0,                          // 没有更早的记录，返回初始值 0
        }
    }
}
```
