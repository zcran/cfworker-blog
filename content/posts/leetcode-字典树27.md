---
title: "leetcode-字典树27"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 找出强数对的最大异或值 I

给你一个下标从 0 开始的整数数组 nums 。如果一对整数 x 和 y 满足以下条件，则称其为 强数对 ：

|x - y| <= min(x, y)
你需要从 nums 中选出两个整数，且满足：这两个整数可以形成一个强数对，并且它们的按位异或（XOR）值是在该数组所有强数对中的 最大值 。

返回数组 nums 所有可能的强数对中的 最大 异或值。

注意，你可以选择同一个整数两次来形成一个强数对。

```
use std::cmp::max;

// ========== Trie 节点定义 ==========
struct TrieNode {
    children: [usize; 2],  // 子节点索引，0 表示不存在
    cnt: i32,              // 子树中的数字个数
}

impl TrieNode {
    fn new() -> Self {
        Self {
            children: [0, 0],
            cnt: 0,
        }
    }
}

// ========== Trie 实现（使用 Vec 池化，比指针更快）==========
struct Trie {
    nodes: Vec<TrieNode>,  // 节点池，索引 0 是根节点
}

impl Trie {
    // 最高位：根据题目数据范围确定
    // nums[i] <= 10^6 < 2^20，所以 19 位足够
    // 但为了安全，使用 20 位（0-20 共 21 位）
    const HIGH_BIT: i32 = 20;

    fn new() -> Self {
        Self {
            nodes: vec![TrieNode::new()],  // 根节点索引为 0
        }
    }

    // 获取或创建子节点，返回子节点索引
    fn get_child(&mut self, parent: usize, bit: usize) -> usize {
        let child = self.nodes[parent].children[bit];
        if child != 0 {
            return child;
        }
        // 创建新节点
        let new_idx = self.nodes.len();
        self.nodes.push(TrieNode::new());
        self.nodes[parent].children[bit] = new_idx;
        new_idx
    }

    // 插入数字
    fn insert(&mut self, val: i32) {
        let mut node = 0;
        for shift in (0..=Self::HIGH_BIT).rev() {
            let bit = ((val >> shift) & 1) as usize;
            node = self.get_child(node, bit);
            self.nodes[node].cnt += 1;
        }
    }

    // 删除数字（只减少计数，不删除节点）
    fn remove(&mut self, val: i32) {
        let mut node = 0;
        for shift in (0..=Self::HIGH_BIT).rev() {
            let bit = ((val >> shift) & 1) as usize;
            node = self.nodes[node].children[bit];
            self.nodes[node].cnt -= 1;
        }
    }

    // 查询与 val 异或的最大值（要求 Trie 非空）
    fn max_xor(&self, val: i32) -> i32 {
        let mut node = 0;
        let mut ans = 0;
        for shift in (0..=Self::HIGH_BIT).rev() {
            let bit = ((val >> shift) & 1) as usize;
            let prefer = bit ^ 1;  // 期望相反的位

            // 检查首选子节点是否存在且有计数
            let prefer_child = self.nodes[node].children[prefer];
            if prefer_child != 0 && self.nodes[prefer_child].cnt > 0 {
                ans |= 1 << shift;  // 这一位异或结果为 1
                node = prefer_child;
            } else {
                node = self.nodes[node].children[bit];
            }
        }
        ans
    }
}

impl Solution {
    pub fn maximum_strong_pair_xor(nums: Vec<i32>) -> i32 {
        // 1. 排序：利用单调性维护窗口
        let mut nums = nums;
        nums.sort_unstable();

        let mut trie = Trie::new();
        let mut ans = 0;
        let mut left = 0;  // 窗口左边界

        // 2. 遍历每个数字作为强数对中的较大值 y
        for &y in &nums {
            // 将 y 插入 Trie
            trie.insert(y);

            // 3. 维护窗口：所有满足 y <= 2*x 的数字（即 x >= ceil(y/2)）
            // 由于数组已排序，只需移动左边界
            while nums[left] * 2 < y {
                trie.remove(nums[left]);
                left += 1;
            }

            // 4. 查询当前窗口内与 y 异或的最大值
            // 注意：窗口至少包含 y 本身，所以 Trie 非空
            let cur_xor = trie.max_xor(y);
            ans = max(ans, cur_xor);
        }

        ans
    }
}
```
