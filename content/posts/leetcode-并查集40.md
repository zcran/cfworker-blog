---
title: "leetcode-并查集40"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 处理含限制条件的好友请求

给你一个整数 n ，表示网络上的用户数目。每个用户按从 0 到 n - 1 进行编号。

给你一个下标从 0 开始的二维整数数组 restrictions ，其中 restrictions[i] = [xi, yi] 意味着用户 xi 和用户 yi 不能 成为 朋友 ，不管是 直接 还是通过其他用户 间接 。

最初，用户里没有人是其他用户的朋友。给你一个下标从 0 开始的二维整数数组 requests 表示好友请求的列表，其中 requests[j] = [uj, vj] 是用户 uj 和用户 vj 之间的一条好友请求。

如果 uj 和 vj 可以成为 朋友 ，那么好友请求将会 成功 。每个好友请求都会按列表中给出的顺序进行处理（即，requests[j] 会在 requests[j + 1] 前）。一旦请求成功，那么对所有未来的好友请求而言， uj 和 vj 将会 成为直接朋友 。

返回一个 布尔数组 result ，其中元素遵循此规则：如果第 j 个好友请求 成功 ，那么 result[j] 就是 true ；否则，为 false 。

注意：如果 uj 和 vj 已经是直接朋友，那么他们之间的请求将仍然 成功 。


```
use std::collections::HashSet;

impl Solution {
    pub fn friend_requests(n: i32, restrictions: Vec<Vec<i32>>, requests: Vec<Vec<i32>>) -> Vec<bool> {
        let n = n as usize;
        let mut parent = (0..n).collect::<Vec<_>>();
        let mut rank = vec![0; n];

        // 并查集查找（路径压缩）
        fn find(parent: &mut [usize], x: usize) -> usize {
            if parent[x] != x {
                parent[x] = find(parent, parent[x]);
            }
            parent[x]
        }

        // 每个连通块（根）的禁止集合，存储的是根节点
        let mut blocked = vec![HashSet::new(); n];

        // 初始化限制关系（每个节点初始是单独的连通块）
        for r in &restrictions {
            let a = r[0] as usize;
            let b = r[1] as usize;
            blocked[a].insert(b);
            blocked[b].insert(a);
        }

        let mut result = Vec::with_capacity(requests.len());

        for req in requests {
            let a = req[0] as usize;
            let b = req[1] as usize;
            let ra = find(&mut parent, a);
            let rb = find(&mut parent, b);

            // 如果已经在同一连通块，请求必然成功
            if ra == rb {
                result.push(true);
                continue;
            }

            // 检查两个连通块之间是否有直接限制（块间限制）
            let can_friend = !blocked[ra].contains(&rb);

            result.push(can_friend);

            if can_friend {
                // 按秩合并，选择新根
                let (new_root, old_root) = if rank[ra] < rank[rb] {
                    // rb 作为新根，合并 ra
                    parent[ra] = rb;
                    if rank[ra] == rank[rb] {
                        rank[rb] += 1;
                    }
                    (rb, ra)
                } else {
                    parent[rb] = ra;
                    if rank[ra] == rank[rb] {
                        rank[ra] += 1;
                    }
                    (ra, rb)
                };

                // 将旧根的限制集合合并到新根，并更新外部节点的指向为新根
                // 注意：旧根的限制集合中存储的是根节点，但可能有些根已经改变，需重新查找
                let old_blocked = blocked[old_root].clone();
                for &node in old_blocked.iter() {
                    let root_node = find(&mut parent, node); // 获取节点当前所在根
                    if root_node != new_root {
                        blocked[new_root].insert(root_node);
                        blocked[root_node].insert(new_root);
                    }
                }
                // 清空旧根的限制集合
                blocked[old_root].clear();
            }
        }

        result
    }
}
```
