---
title: "leetcode-数论52"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 统计最小公倍数图中的连通块数目

给你一个长度为 n 的整数数组 nums 和一个 正 整数 threshold 。

有一张 n 个节点的图，其中第 i 个节点的值为 nums[i] 。如果两个节点对应的值满足 lcm(nums[i], nums[j]) <= threshold ，那么这两个节点在图中有一条 无向 边连接。

请你返回这张图中 连通块 的数目。

一个 连通块 指的是一张图中的一个子图，子图中任意两个节点都存在路径相连，且子图中没有任何一个节点与子图以外的任何节点有边相连。

lcm(a, b) 的意思是 a 和 b 的 最小公倍数 。


```
impl Solution {
    pub fn count_components(nums: Vec<i32>, threshold: i32) -> i32 {
        let n = nums.len();
        let threshold = threshold as usize;

        // 并查集父节点数组
        let mut parent: Vec<usize> = (0..n).collect();
        let mut size = vec![1; n]; // 按秩合并优化
        let mut components = n as i32;

        // 查找根节点（路径压缩）
        fn find(parent: &mut Vec<usize>, x: usize) -> usize {
            if parent[x] != x {
                parent[x] = find(parent, parent[x]);
            }
            parent[x]
        }

        // 合并两个节点
        fn union(parent: &mut Vec<usize>, size: &mut Vec<usize>, a: usize, b: usize) -> bool {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra == rb {
                return false;
            }
            // 按秩合并：将小树合并到大树
            if size[ra] < size[rb] {
                parent[ra] = rb;
                size[rb] += size[ra];
            } else {
                parent[rb] = ra;
                size[ra] += size[rb];
            }
            true
        }

        // 记录每个数字对应的节点索引（只记录小于等于 threshold 的数）
        let mut index_map = vec![usize::MAX; threshold + 1];
        for (i, &val) in nums.iter().enumerate() {
            if val as usize <= threshold {
                index_map[val as usize] = i;
            }
        }

        // 枚举所有可能的公因数 g
        for g in 1..=threshold {
            // 找到第一个大于等于 g 且存在于数组中的数
            let mut first_val = None;
            let mut val = g;
            while val <= threshold {
                if index_map[val] != usize::MAX {
                    first_val = Some(val);
                    break;
                }
                val += g;
            }

            let first_val = match first_val {
                Some(v) => v,
                None => continue, // 不存在任何倍数为数组元素
            };

            let root = find(&mut parent, index_map[first_val]);

            // 计算上界：对于任意数 y，需要满足 lcm(x, y) <= threshold
            // 即 x * y / g <= threshold => y <= g * threshold / x
            let upper = (g as i64 * threshold as i64 / first_val as i64) as usize;

            // 枚举所有倍数 y = first_val + g, first_val + 2g, ...
            let mut y = first_val + g;
            while y <= upper {
                if index_map[y] != usize::MAX {
                    let idx = index_map[y];
                    if union(&mut parent, &mut size, index_map[first_val], idx) {
                        components -= 1;
                    }
                }
                y += g;
            }
        }

        components
    }
}
```
