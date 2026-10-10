---
title: "leetcode-哈希表57"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 设计 SQL

给定两个字符串数组 names 和 columns，大小都为 n。其中 names[i] 是第 i 个表的名称，columns[i] 是第 i 个表的列数。

您需要实现一个支持以下 操作 的类：

· 在特定的表中 插入 一行。插入的每一行都有一个 id。id 是使用自动递增方法分配的，其中第一个插入行的 id 为 1，同一个表中的后续其他行的 id 为上一个插入行的 id (即使它已被删除) 加 1。
· 从指定表中 删除 一行。注意，删除一行 不会 影响下一个插入行的 id。
· 从任何表中 查询 一个特定的单元格并返回其值。
· 从任何表以 csv 格式 导出 所有行。

实现 SQL 类:

· SQL(String[] names, int[] columns)
    · 创建 n 个表。
· bool ins(String name, String[] row)
    · 将 row 插入表 name 中并返回 true。
    · 如果 row.length 不 匹配列的预期数量，或者 name 不是 一个合法的表，不进行任何插入并返回 false。
· void rmv(String name, int rowId, int columnId)
    · 从表 name 中移除行 rowId。
    · 如果 name 不是 一个合法的表或者没有 id 为 rowId 的行，不进行删除。
· String sel(String name, int rowId, int columnId)
    · 返回表 name 中位于特定的 rowId 和 columnId 的单元格的值。
    · 如果 name 不是 一个合法的表，或者单元格 (rowId, columnId) 不合法，返回 "<null>"。
· String[] exp(String name)
    · 返回表 name 中出现的行。
    · 如果 name 不是 一个合法的表，返回一个空数组。每一行以字符串表示，每个单元格的值（包括 行的 id）以 "," 分隔。


```
use std::collections::HashMap;

struct Table {
    /// 该表下一个要分配的自增 id（从 1 开始）
    next_id: i32,
    /// 该表的列数，用于 insert 时校验
    num_columns: usize,
    /// 行 id -> 该行所有单元格的值
    records: HashMap<i32, Vec<String>>,
}

pub struct SQL {
    tables: HashMap<String, Table>,
}

impl SQL {
    pub fn new(names: Vec<String>, columns: Vec<i32>) -> Self {
        let tables = names
            .into_iter()
            .zip(columns)
            .map(|(name, cols)| {
                (
                    name,
                    Table {
                        next_id: 1,
                        num_columns: cols.max(0) as usize,
                        records: HashMap::new(),
                    },
                )
            })
            .collect();
        Self { tables }
    }

    pub fn ins(&mut self, name: String, row: Vec<String>) -> bool {
        let Some(table) = self.tables.get_mut(&name) else {
            return false;
        };

        // 列数不匹配则拒绝插入
        if row.len() != table.num_columns {
            return false;
        }

        let id = table.next_id;
        table.next_id += 1;
        table.records.insert(id, row);
        true
    }

    pub fn rmv(&mut self, name: String, row_id: i32) {
        if let Some(table) = self.tables.get_mut(&name) {
            table.records.remove(&row_id);
        }
    }

    pub fn sel(&self, name: String, row_id: i32, column_id: i32) -> String {
        self.tables
            .get(&name)
            .and_then(|t| t.records.get(&row_id))
            .and_then(|row| {
                if column_id >= 1 {
                    row.get((column_id - 1) as usize)
                } else {
                    None
                }
            })
            .cloned()
            .unwrap_or_else(|| "<null>".to_string())
    }

    pub fn exp(&self, name: String) -> Vec<String> {
        let Some(table) = self.tables.get(&name) else {
            return Vec::new();
        };

        // 按行 id 升序导出，保证输出有序
        let mut ids: Vec<i32> = table.records.keys().copied().collect();
        ids.sort_unstable();

        ids.into_iter()
            .map(|id| {
                let row = &table.records[&id];
                let mut line = String::with_capacity(row.len() * 4 + 2);
                line.push_str(&id.to_string());
                for cell in row {
                    line.push(',');
                    line.push_str(cell);
                }
                line
            })
            .collect()
    }
}
```
