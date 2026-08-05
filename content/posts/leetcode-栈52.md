---
title: "leetcode-栈52"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 机器人碰撞

现有 n 个机器人，编号从 1 开始，每个机器人包含在路线上的位置、健康度和移动方向。

给你下标从 0 开始的两个整数数组 positions、healths 和一个字符串 directions（directions[i] 为 'L' 表示 向左 或 'R' 表示 向右）。 positions 中的所有整数 互不相同 。

所有机器人以 相同速度 同时 沿给定方向在路线上移动。如果两个机器人移动到相同位置，则会发生 碰撞 。

如果两个机器人发生碰撞，则将 健康度较低 的机器人从路线中 移除 ，并且另一个机器人的健康度 减少 1 。幸存下来的机器人将会继续沿着与之前 相同 的方向前进。如果两个机器人的健康度相同，则将二者都从路线中移除。

请你确定全部碰撞后幸存下的所有机器人的 健康度 ，并按照原来机器人编号的顺序排列。即机器人 1 （如果幸存）的最终健康度，机器人 2 （如果幸存）的最终健康度等。 如果不存在幸存的机器人，则返回空数组。

在不再发生任何碰撞后，请你以数组形式，返回所有剩余机器人的健康度（按机器人输入中的编号顺序）。

注意：位置  positions 可能是乱序的。


```
impl Solution {
    /// 返回碰撞后幸存机器人的健康度（按原始编号顺序）
    ///
    /// # 算法思路
    /// 1. 按位置排序机器人，方便按从左到右的顺序处理
    /// 2. 使用单调栈处理碰撞：
    ///    - 只有栈顶机器人向右（'R'）且当前机器人向左（'L'）时才会碰撞
    ///    - 健康度低者被移除，健康度高者健康度减 1
    ///    - 健康度相等则两者都被移除
    ///    - 碰撞后幸存者继续与栈中其他机器人比较
    /// 3. 最后按原始编号排序，返回健康度
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n log n)，主要为排序开销
    /// - 空间复杂度：O(n)，栈和索引数组
    pub fn survived_robots_healths(
        positions: Vec<i32>,
        healths: Vec<i32>,
        directions: String,
    ) -> Vec<i32> {
        let n = positions.len();
        let directions = directions.as_bytes();

        // 按位置排序得到机器人索引
        let mut indices: Vec<usize> = (0..n).collect();
        indices.sort_unstable_by_key(|&i| positions[i]);

        // 栈存储：原始索引、当前健康度、方向
        let mut stack: Vec<(usize, i32, u8)> = Vec::with_capacity(n);

        for &i in &indices {
            let mut current = (i, healths[i], directions[i]);

            // 当发生碰撞（栈顶向右，当前向左）时循环处理
            while let Some(&(top_idx, top_hp, top_dir)) = stack.last() {
                if top_dir == b'R' && current.2 == b'L' {
                    // 弹出栈顶，准备碰撞处理
                    stack.pop();

                    match top_hp.cmp(&current.1) {
                        std::cmp::Ordering::Greater => {
                            // 栈顶机器人更强：当前机器人被移除，栈顶重新入栈
                            current = (top_idx, top_hp - 1, top_dir);
                        }
                        std::cmp::Ordering::Less => {
                            // 当前机器人更强：栈顶被移除，当前健康度减 1
                            current.1 -= 1;
                            // 继续与下一个栈顶比较
                        }
                        std::cmp::Ordering::Equal => {
                            // 健康度相等：两者都被移除
                            current.1 = -1; // 标记为无效
                            break;
                        }
                    }
                } else {
                    // 不会碰撞，退出循环
                    break;
                }
            }

            // 如果当前机器人幸存，入栈
            if current.1 > 0 {
                stack.push(current);
            }
        }

        // 按原始索引排序，提取健康度
        stack.sort_unstable_by_key(|&(idx, _, _)| idx);
        stack.into_iter().map(|(_, hp, _)| hp).collect()
    }
}
```
