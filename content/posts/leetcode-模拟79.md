---
title: "leetcode-模拟79"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 雇佣 K 位工人的总代价

给你一个下标从 0 开始的整数数组 costs ，其中 costs[i] 是雇佣第 i 位工人的代价。

同时给你两个整数 k 和 candidates 。我们想根据以下规则恰好雇佣 k 位工人：

· 总共进行 k 轮雇佣，且每一轮恰好雇佣一位工人。
· 在每一轮雇佣中，从最前面 candidates 和最后面 candidates 人中选出代价最小的一位工人，如果有多位代价相同且最小的工人，选择下标更小的一位工人。
    · 比方说，costs = [3,2,7,7,1,2] 且 candidates = 2 ，第一轮雇佣中，我们选择第 4 位工人，因为他的代价最小 [3,2,7,7,1,2] 。
    · 第二轮雇佣，我们选择第 1 位工人，因为他们的代价与第 4 位工人一样都是最小代价，而且下标更小，[3,2,7,7,2] 。注意每一轮雇佣后，剩余工人的下标可能会发生变化。
· 如果剩余员工数目不足 candidates 人，那么下一轮雇佣他们中代价最小的一人，如果有多位代价相同且最小的工人，选择下标更小的一位工人。
· 一位工人只能被选择一次。

返回雇佣恰好 k 位工人的总代价。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    /// 双堆模拟：左侧堆维护最前面 candidates 个可用工人，
    /// 右侧堆维护最后面 candidates 个可用工人。
    /// 每次从两堆顶取代价最小者（代价相同自然取到下标更小的），
    /// 然后从对应侧补充新的候选人。
    pub fn total_cost(costs: Vec<i32>, k: i32, candidates: i32) -> i64 {
        let n = costs.len();
        let c = candidates as usize;
        let k = k as usize;

        let mut left = BinaryHeap::new();
        let mut right = BinaryHeap::new();

        // 初始化：左侧 [0, c)，右侧 [n-c, n)
        // 若窗口重叠（c*2 >= n），由 max 保证右侧从 c 开始，避免重复入堆
        for i in 0..c.min(n) {
            left.push(Reverse((costs[i], i)));
        }
        for i in (n - c).max(c)..n {
            right.push(Reverse((costs[i], i)));
        }

        // 下一个待补充的索引
        let mut l = c;
        let mut r = n.saturating_sub(c + 1);

        let mut ans = 0i64;
        for _ in 0..k {
            // 决定从哪一侧取人：右侧为空，或左侧堆顶更小/相等（相等时左侧 index 更小）
            let take_left = right.is_empty()
                || left.peek().map_or(false, |&Reverse((cl, _))| {
                    right.peek().map_or(true, |&Reverse((cr, _))| cl <= cr)
                });

            let (cost, _) = if take_left {
                let Reverse((c, i)) = left.pop().unwrap();
                if l <= r {
                    left.push(Reverse((costs[l], l)));
                    l += 1;
                }
                (c, i)
            } else {
                let Reverse((c, i)) = right.pop().unwrap();
                if l <= r {
                    right.push(Reverse((costs[r], r)));
                    r -= 1;
                }
                (c, i)
            };

            ans += cost as i64;
        }

        ans
    }
}
```
