---
title: "leetcode-堆74"
date: 2026-08-21T09:54:15+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 环形闯关游戏

「力扣挑战赛」中有一个由 N 个关卡组成的环形闯关游戏，关卡编号为 0~N-1，编号 0 的关卡和编号 N-1 的关卡相邻。每个关卡均有积分要求，challenge[i] 表示挑战编号 i 的关卡最少需要拥有的积分。

小扣想要挑战关卡，闯关具体规则如下：

· 初始小扣可以指定其中一个关卡为「开启」状态，其余关卡将处于「未开启」状态。
· 小扣可以挑战处于「开启」状态且满足最少积分要求的关卡，若小扣挑战该关卡前积分为 score，挑战结束后，积分将增长为 score|challenge[i]（即位运算中的 "OR" 运算）
· 在挑战某个关卡后，该关卡两侧相邻的关卡将会开启（若之前未开启）

请帮助小扣进行计算，初始最少需要多少积分，可以挑战 环形闯关游戏 的所有关卡。


```
use std::cmp::Ordering;
use std::collections::BinaryHeap;

/// 环形闯关游戏解法 - 基于贪心合并 + 并查集 + 优先队列
///
/// 核心思路：
/// 1. 将每个关卡视为一个线段，初始每个线段只包含自己
/// 2. 每次选择需求分数最小的线段，尝试向左右扩展
/// 3. 如果当前分数不足以扩展，则提升初始分数
/// 4. 使用并查集维护合并后的线段集合
/// 5. 使用优先队列每次取出分数最小的线段

impl Solution {
    pub fn ring_game(challenge: Vec<i64>) -> i64 {
        let n = challenge.len();
        if n == 0 {
            return 0;
        }

        // ---------- 1. 数据结构 ----------
        // 并查集数组
        let mut parent: Vec<usize> = (0..n).collect();

        // 线段信息
        let mut stats = vec![SegmentInfo::new(); n];
        for i in 0..n {
            stats[i] = SegmentInfo {
                l: i,
                r: i,
                now_score: challenge[i],
                start_score: challenge[i],
            };
        }

        // 并查集查找函数（路径压缩）
        fn find(parent: &mut Vec<usize>, x: usize) -> usize {
            if parent[x] != x {
                parent[x] = find(parent, parent[x]);
            }
            parent[x]
        }

        // ---------- 2. 优先队列 ----------
        // 使用最小堆，存储 (start_score, index)
        let mut heap = BinaryHeap::new();
        for i in 0..n {
            // 使用负数实现最小堆
            heap.push(HeapItem {
                score: -stats[i].start_score,
                index: i,
            });
        }

        // 辅助函数：前驱和后继（环形）
        let prev = |x: usize, n: usize| if x == 0 { n - 1 } else { x - 1 };
        let next = |x: usize, n: usize| if x == n - 1 { 0 } else { x + 1 };

        // ---------- 3. 主循环 ----------
        while let Some(HeapItem { score: _, index }) = heap.pop() {
            let x = index;

            // 当前节点不是根节点，已被合并，跳过
            if find(&mut parent, x) != x {
                continue;
            }

            // 所有线段已合并为一个
            if next(stats[x].r, n) == stats[x].l {
                break;
            }

            let left = prev(stats[x].l, n);
            let right = next(stats[x].r, n);

            // 选择相邻线段中需求分数较小的
            let (choice, compact) = if challenge[left] <= challenge[right] {
                (left, challenge[left])
            } else {
                (right, challenge[right])
            };

            // ---------- 4. 无法直接合并，提升初始分数 ----------
            if compact > stats[x].now_score {
                // 计算使 now_score | y >= compact 的最小的 y
                let min_start = Self::calculate_min_start(stats[x].now_score, compact);

                // 调整初始分数
                let adjusted = if stats[x].start_score >= min_start {
                    // 抹去 start_score 中比 min_start 最低位更低的 1
                    let lowest_bit = min_start & (-min_start);
                    let mask = lowest_bit - 1;
                    min_start + (stats[x].start_score & !mask)
                } else {
                    min_start
                };

                // 如果调整后的初始值小于边界线段的初始值，则更新并重新入队
                if adjusted < stats[choice].start_score {
                    stats[x].start_score = adjusted;
                    stats[x].now_score |= adjusted;
                    heap.push(HeapItem {
                        score: -stats[x].start_score,
                        index: x,
                    });
                }
                continue;
            }

            // ---------- 5. 可以合并 ----------
            let choice_root = find(&mut parent, choice);
            let x_root = find(&mut parent, x);

            // 合并到 x 的根
            parent[choice_root] = x_root;

            // 更新线段信息
            stats[x_root].now_score |= stats[choice_root].now_score;

            // 判断合并的是左边还是右边
            if next(stats[choice_root].r, n) == stats[x_root].l {
                stats[x_root].l = stats[choice_root].l;
            } else {
                stats[x_root].r = stats[choice_root].r;
            }

            // 重新入队
            heap.push(HeapItem {
                score: -stats[x_root].start_score,
                index: x_root,
            });
        }

        // ---------- 6. 返回结果 ----------
        let root = find(&mut parent, 1);
        stats[root].start_score
    }

    /// 计算使 now | y >= target 的最小的 y
    ///
    /// 算法：逐位比较 now 和 target
    /// - 如果 now 的位为 1，target 的位为 0，则后续位无需再设置，直接退出
    /// - 如果 now 的位为 0，target 的位为 1，则 y 的该位必须为 1
    /// - 其他情况 y 的该位为 0
    fn calculate_min_start(now: i64, target: i64) -> i64 {
        let mut min_start = 0i64;
        for i in (0..=63).rev() {
            let now_bit = (now >> i) & 1;
            let target_bit = (target >> i) & 1;

            if now_bit == 1 && target_bit == 0 {
                // now 的这一位已经是 1，target 是 0，不需要再设更高的位
                break;
            }
            if now_bit == 0 && target_bit == 1 {
                // 必须设置这一位
                min_start |= 1i64 << i;
            }
            // now_bit == 1 && target_bit == 1: y 该位为 0
            // now_bit == 0 && target_bit == 0: y 该位为 0
        }
        min_start
    }
}

// ============ 数据结构 ============

/// 线段信息
#[derive(Clone)]
struct SegmentInfo {
    l: usize,           // 左边界
    r: usize,           // 右边界
    now_score: i64,     // 当前分数（合并后的 OR 值）
    start_score: i64,   // 初始分数
}

impl SegmentInfo {
    fn new() -> Self {
        Self {
            l: 0,
            r: 0,
            now_score: 0,
            start_score: 0,
        }
    }
}

/// 优先队列项（最小堆）
#[derive(Eq, PartialEq)]
struct HeapItem {
    score: i64,   // 使用负数实现最小堆
    index: usize,
}

impl Ord for HeapItem {
    fn cmp(&self, other: &Self) -> Ordering {
        // 注意：我们想要最小堆，所以比较时反转顺序
        // 先比较分数（越小优先级越高），再比较索引
        match self.score.cmp(&other.score) {
            Ordering::Equal => self.index.cmp(&other.index),
            other => other, // 反转：因为 BinaryHeap 是最大堆
        }
    }
}

impl PartialOrd for HeapItem {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}
```
