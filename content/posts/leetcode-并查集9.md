---
title: "leetcode-并查集9"
date: 2026-06-25T11:22:21+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 情侣牵手

n 对情侣坐在连续排列的 2n 个座位上，想要牵到对方的手。

人和座位由一个整数数组 row 表示，其中 row[i] 是坐在第 i 个座位上的人的 ID。情侣们按顺序编号，第一对是 (0, 1)，第二对是 (2, 3)，以此类推，最后一对是 (2n - 2, 2n - 1)。

返回 最少交换座位的次数，以便每对情侣可以并肩坐在一起。 每次交换可选择任意两人，让他们站起来交换座位。


```
impl Solution {
    pub fn min_swaps_couples(row: Vec<i32>) -> i32 {
        let n = row.len() / 2; // 情侣对数
        let mut parent = (0..n).collect::<Vec<_>>(); // 并查集，每个节点代表一对情侣
        let mut size = vec![1; n]; // 集合大小，用于按秩合并

        /// 查找根节点，带路径压缩
        fn find(parent: &mut [usize], mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        }

        /// 合并两个集合
        fn union(parent: &mut [usize], size: &mut [usize], x: usize, y: usize) {
            let rx = find(parent, x);
            let ry = find(parent, y);
            if rx == ry { return; }

            // 按秩合并
            if size[rx] < size[ry] {
                parent[rx] = ry;
                size[ry] += size[rx];
            } else {
                parent[ry] = rx;
                size[rx] += size[ry];
            }
        }

        // 遍历每对座位，将坐在同一对座位上的两个人所属的情侣对合并
        for i in (0..row.len()).step_by(2) {
            let couple_a = (row[i] / 2) as usize;      // 第 i 个座位的人属于哪对情侣
            let couple_b = (row[i + 1] / 2) as usize;  // 第 i+1 个座位的人属于哪对情侣
            union(&mut parent, &mut size, couple_a, couple_b);
        }

        // 每个连通分量中，需要交换的次数 = 分量大小 - 1
        // 总交换次数 = n - 连通分量数量
        let mut components = 0;
        for i in 0..n {
            if find(&mut parent, i) == i {
                components += 1;
            }
        }

        (n - components) as i32
    }
}
```
