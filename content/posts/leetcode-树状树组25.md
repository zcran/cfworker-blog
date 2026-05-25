---
title: "leetcode-树状树组25"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 交替组 III

给你一个整数数组 colors 和一个二维整数数组 queries 。colors表示一个由红色和蓝色瓷砖组成的环，第 i 块瓷砖的颜色为 colors[i] ：

· colors[i] == 0 表示第 i 块瓷砖的颜色是 红色 。
· colors[i] == 1 表示第 i 块瓷砖的颜色是 蓝色 。

环中连续若干块瓷砖的颜色如果是 交替 颜色（也就是说这组瓷砖中除了第一块和最后一块瓷砖以外，中间瓷砖的颜色与它 左边 和 右边 的颜色都不同），那么它被称为一个 交替组。

你需要处理两种类型的查询：

· queries[i] = [1, sizei]，确定大小为sizei的 交替组 的数量。
· queries[i] = [2, indexi, colori]，将colors[indexi]更改为colori。

返回数组 answer，数组中按顺序包含第一种类型查询的结果。

注意 ，由于 colors 表示一个 环 ，第一块 瓷砖和 最后一块 瓷砖是相邻的。

```
use std::collections::BTreeSet;

/// 树状数组（Fenwick Tree），维护两个信息：元素个数和元素和。
struct FenwickTree {
    tree: Vec<[i32; 2]>, // [计数, 和]
    n: usize,
}

impl FenwickTree {
    fn new(n: usize) -> Self {
        Self {
            tree: vec![[0, 0]; n + 1],
            n,
        }
    }

    /// 在长度 size 上增加 op（+1 添加，-1 删除）
    fn update(&mut self, size: usize, op: i32) {
        if size == 0 {
            return;
        }
        let mut i = self.tree.len() - size;
        while i < self.tree.len() {
            self.tree[i][0] += op;
            self.tree[i][1] += op * size as i32;
            i += i & i.wrapping_neg(); // lowbit
        }
    }

    /// 查询长度 ≥ size 的所有段的总个数和总长度
    fn query(&self, size: usize) -> (i32, i32) {
        let mut cnt = 0;
        let mut sum = 0;
        let mut i = self.tree.len() - size;
        while i > 0 {
            cnt += self.tree[i][0];
            sum += self.tree[i][1];
            i &= i - 1;
        }
        (cnt, sum)
    }
}

impl Solution {
    pub fn number_of_alternating_groups(mut a: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = a.len();
        let mut ends = BTreeSet::new();
        let mut bit = FenwickTree::new(n);

        // ----- 辅助函数：环形前驱（非空集合）-----
        fn predecessor(set: &BTreeSet<usize>, i: usize) -> usize {
            set.range(..i)
                .next_back()
                .copied()
                .unwrap_or_else(|| *set.last().unwrap())
        }

        // ----- 辅助函数：环形后继（非空集合，排除自身）-----
        fn successor(set: &BTreeSet<usize>, i: usize) -> usize {
            set.range(i + 1..)
                .next()
                .copied()
                .unwrap_or_else(|| *set.first().unwrap())
        }

        // ----- 添加结束位置 -----
        fn add_end(ends: &mut BTreeSet<usize>, bit: &mut FenwickTree, n: usize, i: usize) {
            if ends.is_empty() {
                bit.update(n, 1);
            } else {
                let pre = predecessor(ends, i);
                let nxt = successor(ends, i);
                // 移除旧段 pre -> nxt
                let old_len = (nxt + n - pre - 1) % n + 1;
                bit.update(old_len, -1);
                // 添加两个新段：pre -> i 和 i -> nxt
                let len1 = (i + n - pre) % n;
                let len2 = (nxt + n - i) % n;
                if len1 > 0 {
                    bit.update(len1, 1);
                }
                if len2 > 0 {
                    bit.update(len2, 1);
                }
            }
            ends.insert(i);
        }

        // ----- 移除结束位置 -----
        fn remove_end(ends: &mut BTreeSet<usize>, bit: &mut FenwickTree, n: usize, i: usize) {
            if ends.len() == 1 {
                bit.update(n, -1);
            } else {
                let pre = predecessor(ends, i);
                let nxt = successor(ends, i);
                let len1 = (i + n - pre) % n;
                let len2 = (nxt + n - i) % n;
                if len1 > 0 {
                    bit.update(len1, -1);
                }
                if len2 > 0 {
                    bit.update(len2, -1);
                }
                let new_len = (nxt + n - pre - 1) % n + 1;
                bit.update(new_len, 1);
            }
            ends.remove(&i);
        }

        // 初始化：根据初始数组建立所有结束位置
        for i in 0..n {
            if a[i] == a[(i + 1) % n] {
                add_end(&mut ends, &mut bit, n, i);
            }
        }

        let mut ans = Vec::new();

        // 处理查询
        for q in queries {
            match q[0] {
                1 => {
                    let k = q[1] as usize;
                    if ends.is_empty() {
                        // 整个环完全交替，任何长度为 k 的子数组都符合条件
                        ans.push(n as i32);
                    } else {
                        let (cnt, sum) = bit.query(k);
                        // 每个长度为 len 的段贡献 max(0, len - k + 1)
                        let res = sum - cnt * (k as i32 - 1);
                        ans.push(res);
                    }
                }
                2 => {
                    let idx = q[1] as usize;
                    let new_val = q[2];
                    if a[idx] == new_val {
                        continue;
                    }
                    let prev = (idx + n - 1) % n;
                    let next = (idx + 1) % n;

                    // 修改前，移除受影响的结束位置
                    if a[prev] == a[idx] {
                        remove_end(&mut ends, &mut bit, n, prev);
                    }
                    if a[idx] == a[next] {
                        remove_end(&mut ends, &mut bit, n, idx);
                    }

                    // 执行修改
                    a[idx] = new_val;

                    // 修改后，添加新的结束位置
                    if a[prev] == a[idx] {
                        add_end(&mut ends, &mut bit, n, prev);
                    }
                    if a[idx] == a[next] {
                        add_end(&mut ends, &mut bit, n, idx);
                    }
                }
                _ => unreachable!(),
            }
        }

        ans
    }
}
```
