---
title: "leetcode-哈希函数5"
date: 2026-04-26T20:59:32+08:00
tags: ["leetcode", "哈希函数"]
draft: false
---


## 删除系统中的重复文件夹

由于一个漏洞，文件系统中存在许多重复文件夹。给你一个二维数组 paths，其中 paths[i] 是一个表示文件系统中第 i 个文件夹的绝对路径的数组。

例如，["one", "two", "three"] 表示路径 "/one/two/three" 。
如果两个文件夹（不需要在同一层级）包含 非空且相同的 子文件夹 集合 并具有相同的子文件夹结构，则认为这两个文件夹是相同文件夹。相同文件夹的根层级 不 需要相同。如果存在两个（或两个以上）相同 文件夹，则需要将这些文件夹和所有它们的子文件夹 标记 为待删除。

例如，下面文件结构中的文件夹 "/a" 和 "/b" 相同。它们（以及它们的子文件夹）应该被 全部 标记为待删除：
/a
/a/x
/a/x/y
/a/z
/b
/b/x
/b/x/y
/b/z
然而，如果文件结构中还包含路径 "/b/w" ，那么文件夹 "/a" 和 "/b" 就不相同。注意，即便添加了新的文件夹 "/b/w" ，仍然认为 "/a/x" 和 "/b/x" 相同。
一旦所有的相同文件夹和它们的子文件夹都被标记为待删除，文件系统将会 删除 所有上述文件夹。文件系统只会执行一次删除操作。执行完这一次删除操作后，不会删除新出现的相同文件夹。

返回二维数组 ans ，该数组包含删除所有标记文件夹之后剩余文件夹的路径。路径可以按 任意顺序 返回。

```
use std::collections::hash_map::DefaultHasher;
use std::collections::HashMap;
use std::hash::{Hasher};

/// 树节点，表示一个文件夹或子文件夹结构。
#[derive(Debug)]
struct Entry {
    /// 叶子节点对应的原始路径索引（非叶子节点为 INVALID_INDEX）
    idx: usize,
    /// 子树哈希（不包含当前节点名的哈希，仅基于子节点结构）
    hash: u64,
    /// 子节点映射：文件夹名 -> 子节点
    children: HashMap<String, Box<Entry>>,
}

/// 无效索引，用于非叶子节点
const INVALID_INDEX: usize = 20001;

impl Solution {
    /// 递归计算并存储每个节点的子树哈希值。
    /// 哈希规则：空子树返回0；非空子树对每个子节点 `(文件夹名, 子节点哈希)` 排序后整体哈希。
    fn compute_hash(entry: &mut Entry) -> u64 {
        if entry.children.is_empty() {
            entry.hash = 0;
            return 0;
        }

        // 1. 递归计算所有子节点的哈希，并收集 (name, child_hash)
        let mut child_data: Vec<(String, u64)> = entry.children
            .iter_mut()
            .map(|(name, child)| (name.clone(), Self::compute_hash(child)))
            .collect();

        // 2. 排序保证确定性
        child_data.sort_by(|a, b| a.0.cmp(&b.0));

        // 3. 依次写入哈希器
        let mut hasher = DefaultHasher::new();
        for (name, h) in child_data {
            hasher.write(name.as_bytes());
            hasher.write_u64(h);
        }
        entry.hash = hasher.finish();
        entry.hash
    }

    /// 统计每个哈希值在树中出现的频次（深度优先）。
    fn count_hashes(entry: &Entry, freq: &mut HashMap<u64, u32>) {
        *freq.entry(entry.hash).or_insert(0) += 1;
        for child in entry.children.values() {
            Self::count_hashes(child, freq);
        }
    }

    /// 标记需要保留的路径索引。
    /// 规则：如果某节点的子树哈希只出现一次（全局唯一），则该节点及其所有子节点均保留；
    ///       否则，该节点被删除，其子树全部删除（不再递归）。
    /// 叶子节点的保留状态取决于 `keep_parent`（若父节点保留则叶子保留）。
    fn mark_keep(
        entry: &Entry,
        keep: &mut Vec<bool>,
        freq: &HashMap<u64, u32>,
        parent_kept: bool,
    ) {
        // 叶子节点：直接将父节点的保留状态传播给它
        if entry.children.is_empty() {
            if entry.idx != INVALID_INDEX {
                keep[entry.idx] = parent_kept;
            }
            return;
        }

        // 非叶子节点：判断自身是否唯一
        let is_unique = freq.get(&entry.hash) == Some(&1);
        let keep_self = parent_kept && (entry.idx == INVALID_INDEX || is_unique);
        if entry.idx != INVALID_INDEX {
            keep[entry.idx] = keep_self;
        }

        // 递归子节点：只有当前节点保留时，子节点才可能保留（且子节点自身的唯一性决定其保留状态）
        for child in entry.children.values() {
            Self::mark_keep(child, keep, freq, keep_self);
        }
    }

    /// 主函数：删除重复文件夹结构。
    /// 返回所有应当保留的路径（即去除重复子树后的路径集合）。
    pub fn delete_duplicate_folder(paths: Vec<Vec<String>>) -> Vec<Vec<String>> {
        // ----- 1. 构建树 -----
        let mut root = Entry {
            idx: INVALID_INDEX,
            hash: 0,
            children: HashMap::new(),
        };

        for (path_idx, segments) in paths.iter().enumerate() {
            let leaf_idx = segments.len() - 1;
            let mut cur = &mut root;
            for (i, seg) in segments.iter().enumerate() {
                cur = cur.children
                    .entry(seg.clone())
                    .or_insert_with(|| Box::new(Entry {
                        idx: INVALID_INDEX,
                        hash: 0,
                        children: HashMap::new(),
                    }));
                if i == leaf_idx {
                    cur.idx = path_idx;   // 叶子节点记录原始路径索引
                }
            }
        }

        // ----- 2. 计算哈希 -----
        Self::compute_hash(&mut root);

        // ----- 3. 统计哈希频次 -----
        let mut freq = HashMap::new();
        Self::count_hashes(&root, &mut freq);

        // ----- 4. 标记保留路径 -----
        let mut keep = vec![false; paths.len()];
        for child in root.children.values() {
            Self::mark_keep(child, &mut keep, &freq, true);
        }

        // ----- 5. 收集结果 -----
        paths.into_iter()
            .enumerate()
            .filter(|(idx, _)| keep[*idx])
            .map(|(_, path)| path)
            .collect()
    }
}
```
