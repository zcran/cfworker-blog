---
title: "leetcode-树9"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 最大节点价值之和

给你一棵 n 个节点的 无向 树，节点从 0 到 n - 1 编号。树以长度为 n - 1 下标从 0 开始的二维整数数组 edges 的形式给你，其中 edges[i] = [ui, vi] 表示树中节点 ui 和 vi 之间有一条边。同时给你一个 正 整数 k 和一个长度为 n 下标从 0 开始的 非负 整数数组 nums ，其中 nums[i] 表示节点 i 的 价值 。

Alice 想 最大化 树中所有节点价值之和。为了实现这一目标，Alice 可以执行以下操作 任意 次（包括 0 次）：

· 选择连接节点 u 和 v 的边 [u, v] ，并将它们的值更新为：
    · nums[u] = nums[u] XOR k
    · nums[v] = nums[v] XOR k

请你返回 Alice 通过执行以上操作 任意次 后，可以得到所有节点 价值之和 的 最大值 。


```
impl Solution {
    /// 贪心 + 排序
    ///
    /// 核心思路：
    /// 1. 一次操作相当于对两个相邻节点同时异或 k。
    ///    由于树是连通的，任意一对节点都可以通过一系列操作“配对”，
    ///    所以问题等价于：从 nums 中选择若干个节点异或 k（个数必须为偶数），
    ///    使得总和最大。
    /// 2. 对每个节点，令 diff[i] = (nums[i] ^ k) - nums[i]，
    ///    表示该节点异或 k 后对总和的增量（可能为负）。
    /// 3. 为了使总和最大，我们贪心地选择增量最大的两个节点配对（保证偶数个），
    ///    只要这一对的增量和 >= 0 就执行，否则停止。
    ///    因此将 diff 排序后从大到小两两配对即可。
    pub fn maximum_value_sum(nums: Vec<i32>, k: i32, edges: Vec<Vec<i32>>) -> i64 {
        // 注意：edges 参数实际未使用，因为树连通性保证任意偶数节点都可配对
        let _ = edges;

        // 初始总和
        let mut total: i64 = nums.iter().map(|&x| x as i64).sum();

        // 计算每个节点异或 k 后的增量
        let mut diff: Vec<i32> = nums.iter().map(|&x| (x ^ k) - x).collect();
        diff.sort_unstable();

        // 从最大增量开始，两两配对；若一对增量和 >= 0 则累加
        let mut i = diff.len();
        while i >= 2 {
            let pair_sum = diff[i - 1] + diff[i - 2];
            if pair_sum < 0 {
                break;
            }
            total += pair_sum as i64;
            i -= 2;
        }

        total
    }
}
```
