---
title: "leetcode-回溯17"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 单词接龙 II

按字典 wordList 完成从单词 beginWord 到单词 endWord 转化，一个表示此过程的 转换序列 是形式上像 beginWord -> s1 -> s2 -> ... -> sk 这样的单词序列，并满足：

· 每对相邻的单词之间仅有单个字母不同。
· 转换过程中的每个单词 si（1 <= i <= k）必须是字典 wordList 中的单词。注意，beginWord 不必是字典 wordList 中的单词。
· sk == endWord

给你两个单词 beginWord 和 endWord ，以及一个字典 wordList 。请你找出并返回所有从 beginWord 到 endWord 的 最短转换序列 ，如果不存在这样的转换序列，返回一个空列表。每个序列都应该以单词列表 [beginWord, s1, s2, ..., sk] 的形式返回。


```
use std::{collections::VecDeque, mem::swap};

impl Solution {
    /// 寻找从 begin 到 end 的所有最短转换序列
    pub fn find_ladders(begin: String, end: String, mut word_list: Vec<String>) -> Vec<Vec<String>> {
        // 如果目标单词不在字典中，直接返回空
        if !word_list.contains(&end) {
            return Vec::new();
        }

        // 移除 begin 和 end（如果有），然后将它们添加到末尾
        // 这样确保 begin 和 end 都在字典中且位置已知
        word_list.retain(|s| s != &begin && s != &end);
        word_list.extend_from_slice(&[begin, end]);

        // 获取 begin 和 end 在列表中的索引
        let src = word_list.len() - 2; // begin 的索引
        let tgt = word_list.len() - 1; // end 的索引

        Self::find_ladders_brute(&word_list, src, tgt)
    }

    /// 双向 BFS 搜索所有最短路径
    pub fn find_ladders_brute(
        word_list: &[String],
        src: usize,   // begin 的索引
        tgt: usize,   // end 的索引
    ) -> Vec<Vec<String>> {
        // 双向 BFS 队列
        let mut queue_src = vec![src];
        let mut queue_tgt = vec![tgt];

        // 记录当前层已访问的节点
        let mut visited_src = vec![false; word_list.len()];

        // 邻接表：记录从 src 侧和 tgt 侧搜索时每个节点的前驱
        // adj_src[i] 表示从 src 出发 BFS 时，i 的前驱节点列表
        // adj_tgt[i] 表示从 tgt 出发 BFS 时，i 的前驱节点列表
        let mut adj_src: Vec<Vec<usize>> = vec![vec![]; word_list.len()];
        let mut adj_tgt: Vec<Vec<usize>> = vec![vec![]; word_list.len()];
        adj_tgt[tgt] = vec![usize::MAX]; // 标记终点

        let mut found = false;      // 是否找到路径
        let mut swapped = false;    // 是否交换过两侧

        while !queue_src.is_empty() {
            // 扩展 src 侧的一层
            for &i in &queue_src {
                for j in 0..word_list.len() {
                    // 跳过条件：已处理过该节点，或不是相邻单词
                    if (!adj_src[j].is_empty() && !visited_src[j]) || !one_diff(&word_list[i], &word_list[j]) {
                        continue;
                    }

                    // 如果 j 已经被 tgt 侧访问过，说明找到了路径
                    if !adj_tgt[j].is_empty() {
                        found = true;
                    }

                    visited_src[j] = true;
                    adj_src[j].push(i); // 记录前驱
                }
            }

            // 如果找到路径，立即构建结果
            if found {
                return Self::build(
                    word_list, src, tgt, swapped,
                    adj_src, adj_tgt,
                    queue_tgt, visited_src,
                );
            }

            // 准备下一层：收集所有新访问的节点
            queue_src.clear();
            queue_src.extend((0..word_list.len()).filter(|&i| visited_src[i]));
            visited_src.fill(false); // 重置访问标记

            // 平衡双向 BFS：从较小的队列方向继续扩展
            if queue_src.len() >= queue_tgt.len() {
                swap(&mut queue_src, &mut queue_tgt);
                swap(&mut adj_src, &mut adj_tgt);
                swapped = !swapped;
            }
        }

        Vec::new() // 未找到路径
    }

    /// 根据 BFS 记录的父子关系构建所有最短路径
    pub fn build(
        word_list: &[String],
        src: usize,
        tgt: usize,
        swapped: bool,
        mut adj_src: Vec<Vec<usize>>,
        mut adj_tgt: Vec<Vec<usize>>,
        queue_tgt: Vec<usize>,
        visited_src: Vec<bool>,
    ) -> Vec<Vec<String>> {
        // 如果发生过交换，需要交换回来以保证 adj_src 是从 src 出发的方向
        if swapped {
            swap(&mut adj_src, &mut adj_tgt);
        }

        // 从相遇节点开始构建路径（相遇节点是 tgt 侧队列中也被 src 侧访问到的节点）
        let mut paths: VecDeque<Vec<usize>> = queue_tgt
            .into_iter()
            .filter(|&i| visited_src[i])
            .map(|i| vec![i])
            .collect();

        // 从相遇节点回溯到 src（向前扩展）
        while paths[0][0] != src {
            for _ in 0..paths.len() {
                let path = paths.pop_front().unwrap();
                // 对当前路径的第一个节点的所有前驱，构造新路径
                paths.extend(adj_src[path[0]].iter().map(|&prev| {
                    let mut new_path = vec![prev];
                    new_path.extend_from_slice(&path);
                    new_path
                }));
            }
        }

        // 从相遇节点前进到 tgt（向后扩展）
        while *paths[0].last().unwrap() != tgt {
            for _ in 0..paths.len() {
                let path = paths.pop_front().unwrap();
                let last = *path.last().unwrap();
                // 对当前路径最后一个节点的所有后继，构造新路径
                paths.extend(adj_tgt[last].iter().map(|&next| {
                    let mut new_path: Vec<usize> = path.clone();
                    new_path.push(next);
                    new_path
                }));
            }
        }

        // 将索引路径转换为单词路径
        paths
            .into_iter()
            .map(|path| path.into_iter().map(|i| word_list[i].clone()).collect())
            .collect()
    }
}

/// 判断两个字符串是否恰好只有一个字符不同
fn one_diff(a: &str, b: &str) -> bool {
    let mut diff_found = false;
    for i in 0..a.len() {
        if a.as_bytes()[i] != b.as_bytes()[i] {
            // 如果已经遇到过不同的字符，说明这是第二次不同，返回 false
            if diff_found {
                return false;
            }
            diff_found = true;
        }
    }
    diff_found // 恰好有一个不同
}
```
