---
title: "leetcode-模拟82"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 过桥的时间

共有 k 位工人计划将 n 个箱子从右侧的（旧）仓库移动到左侧的（新）仓库。给你两个整数 n 和 k，以及一个二维整数数组 time ，数组的大小为 k x 4 ，其中 time[i] = [righti, picki, lefti, puti] 。

一条河将两座仓库分隔，只能通过一座桥通行。旧仓库位于河的右岸，新仓库在河的左岸。开始时，所有 k 位工人都在桥的左侧等待。为了移动这些箱子，第 i 位工人（下标从 0 开始）可以：

· 从左岸（新仓库）跨过桥到右岸（旧仓库），用时 righti 分钟。
· 从旧仓库选择一个箱子，并返回到桥边，用时 picki 分钟。不同工人可以同时搬起所选的箱子。
· 从右岸（旧仓库）跨过桥到左岸（新仓库），用时 lefti 分钟。
· 将箱子放入新仓库，并返回到桥边，用时 puti 分钟。不同工人可以同时放下所选的箱子。

如果满足下面任一条件，则认为工人 i 的 效率低于 工人 j ：

· lefti + righti > leftj + rightj
· lefti + righti == leftj + rightj 且 i > j

工人通过桥时需要遵循以下规则：

· 同时只能有一名工人过桥。
· 当桥梁未被使用时，优先让右侧 效率最低 的工人（已经拿起盒子的工人）过桥。如果不是，优先让左侧 效率最低 的工人通过。
· 如果左侧已经派出足够的工人来拾取所有剩余的箱子，则 不会 再从左侧派出工人。

请你返回最后一个箱子 到达桥左侧 的时间。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    /// 模拟工人过桥搬箱子。
    ///
    /// 状态：
    /// - wait_l / wait_r：左右两侧等待过桥的工人（效率低的优先，即 left+right 大的优先）
    /// - work_l / work_r：正在搬/放箱子的工人（完成时间早的优先）
    ///
    /// 每轮：
    /// 1. 把当前时刻前已完成工作的工人放回等待队列
    /// 2. 优先让右侧等待的工人过桥（带着箱子到左岸）
    /// 3. 否则若还有箱子，让左侧等待的工人过桥（去右岸搬箱子）
    /// 4. 否则跳到下一个工人完成工作的时刻
    pub fn find_crossing_time(n: i32, k: i32, time: Vec<Vec<i32>>) -> i32 {
        let k = k as usize;
        let mut remain = n;
        let mut cur = 0;

        // 预计算效率值：left + right，越大效率越低
        let eff: Vec<i32> = time.iter().map(|t| t[0] + t[2]).collect();

        // wait: 最大堆，(eff, id) —— 效率低的先过桥
        let mut wait_l: BinaryHeap<(i32, usize)> = (0..k).map(|i| (eff[i], i)).collect();
        let mut wait_r: BinaryHeap<(i32, usize)> = BinaryHeap::new();

        // work: 最小堆，(完成时间, id) —— 先完成的先释放
        let mut work_l: BinaryHeap<Reverse<(i32, usize)>> = BinaryHeap::new();
        let mut work_r: BinaryHeap<Reverse<(i32, usize)>> = BinaryHeap::new();

        while remain > 0 || !work_r.is_empty() || !wait_r.is_empty() {
            // 释放已完成工作的工人到等待队列
            while let Some(&Reverse((t, i))) = work_l.peek() {
                if t <= cur { wait_l.push((eff[i], i)); work_l.pop(); } else { break; }
            }
            while let Some(&Reverse((t, i))) = work_r.peek() {
                if t <= cur { wait_r.push((eff[i], i)); work_r.pop(); } else { break; }
            }

            if let Some((_, id)) = wait_r.pop() {
                // 右侧工人带箱过桥到左岸
                cur += time[id][2];
                work_l.push(Reverse((cur + time[id][3], id)));
            } else if remain > 0 {
                if let Some((_, id)) = wait_l.pop() {
                    // 左侧工人过桥到右岸搬箱
                    cur += time[id][0];
                    work_r.push(Reverse((cur + time[id][1], id)));
                    remain -= 1;
                } else {
                    cur = Self::advance(cur, &work_l, &work_r);
                }
            } else {
                cur = Self::advance(cur, &work_l, &work_r);
            }
        }

        cur
    }

    /// 无人可过桥时，将当前时间推进到最早有工人完成工作的时刻
    fn advance(
        cur: i32,
        work_l: &BinaryHeap<Reverse<(i32, usize)>>,
        work_r: &BinaryHeap<Reverse<(i32, usize)>>,
    ) -> i32 {
        let mut next = i32::MAX;
        if let Some(&Reverse((t, _))) = work_l.peek() { next = next.min(t); }
        if let Some(&Reverse((t, _))) = work_r.peek() { next = next.min(t); }
        if next == i32::MAX { cur } else { cur.max(next) }
    }
}
```
