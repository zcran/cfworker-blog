---
title: "leetcode-有序集合26"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


##  删除操作后的最大子段和


给你两个下标从 0 开始的整数数组 nums 和 removeQueries ，两者长度都为 n 。对于第 i 个查询，nums 中位于下标 removeQueries[i] 处的元素被删除，将 nums 分割成更小的子段。

一个 子段 是 nums 中连续 正 整数形成的序列。子段和 是子段中所有元素的和。

请你返回一个长度为 n 的整数数组 answer ，其中 answer[i]是第 i 次删除操作以后的 最大 子段和。

注意：一个下标至多只会被删除一次。


```
impl Solution {
    pub fn maximum_segment_sum(nums: Vec<i32>, remove_queries: Vec<i32>) -> Vec<i64> {
        let n = nums.len();
        // 并查集：parent[i] 表示 i 的父节点，sum[i] 表示以 i 为根的段的和
        let mut parent: Vec<usize> = (0..=n).collect(); // n 作为哨兵
        let mut sum: Vec<i64> = vec![0; n + 1];
        let mut ans = vec![0; n];

        // 查找根节点（路径压缩）
        fn find(parent: &mut Vec<usize>, x: usize) -> usize {
            if parent[x] != x {
                parent[x] = find(parent, parent[x]);
            }
            parent[x]
        }

        // 逆序处理：从后往前恢复删除的元素
        for i in (0..n-1).rev() {
            let pos = remove_queries[i + 1] as usize;
            let root = find(&mut parent, pos + 1); // 右侧段的根
            parent[pos] = root;                    // 将当前位置合并到右侧
            sum[root] += sum[pos] + nums[pos] as i64;
            ans[i] = ans[i + 1].max(sum[root]);    // 更新最大子段和
        }

        ans
    }
}
```
