---
title: "leetcode-并查集61"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 交换元素后的最大交替和


给你一个整数数组 nums。

你希望最大化 nums 的 交替和：将偶数下标的元素 相加 并 减去 奇数索引的元素获得的值。即 nums[0] - nums[1] + nums[2] - nums[3]...

同时给你一个二维整数数组 swaps，其中 swaps[i] = [pi, qi]。对于 swaps 中的每对 [pi, qi]，你可以交换索引 pi 和 qi 处的元素。这些交换可以进行任意次数和任意顺序。

返回 nums 可能的最大 交替和。

```
struct UnionFind {
    parent: Vec<usize>,
    rank: Vec<usize>,
    odd_count: Vec<usize>, // 每个集合中奇数索引的数量
}

impl UnionFind {
    fn new(n: usize) -> Self {
        Self {
            parent: (0..n).collect(),
            rank: vec![0; n],
            odd_count: (0..n).map(|i| i % 2).collect(),
        }
    }

    fn find(&mut self, x: usize) -> usize {
        if self.parent[x] != x {
            self.parent[x] = self.find(self.parent[x]);
        }
        self.parent[x]
    }

    fn union(&mut self, a: usize, b: usize) {
        let ra = self.find(a);
        let rb = self.find(b);
        if ra == rb {
            return;
        }
        // 按秩合并
        if self.rank[ra] < self.rank[rb] {
            self.parent[ra] = rb;
            self.odd_count[rb] += self.odd_count[ra];
        } else {
            self.parent[rb] = ra;
            self.odd_count[ra] += self.odd_count[rb];
            if self.rank[ra] == self.rank[rb] {
                self.rank[ra] += 1;
            }
        }
    }
}

impl Solution {
    pub fn max_alternating_sum(nums: Vec<i32>, swaps: Vec<Vec<i32>>) -> i64 {
        let n = nums.len();
        let mut uf = UnionFind::new(n);

        // 合并可交换的索引
        for swap in swaps {
            let a = swap[0] as usize;
            let b = swap[1] as usize;
            uf.union(a, b);
        }

        // 按连通分量分组，收集每个分组的元素
        let mut groups = vec![Vec::new(); n];
        for i in 0..n {
            let root = uf.find(i);
            groups[root].push(nums[i] as i64);
        }

        let mut ans = 0;

        // 对每个连通分量独立计算最大交替和
        for i in 0..n {
            if groups[i].is_empty() {
                continue;
            }

            // 升序排序：小的放奇数位置（被减），大的放偶数位置（被加）
            groups[i].sort_unstable();

            let odd_count = uf.odd_count[i]; // 该分量中奇数索引的数量

            // 前 odd_count 个最小的元素放在奇数位置（减去）
            // 剩余的元素放在偶数位置（加上）
            for j in 0..groups[i].len() {
                if j < odd_count {
                    ans -= groups[i][j];
                } else {
                    ans += groups[i][j];
                }
            }
        }

        ans
    }
}
```
