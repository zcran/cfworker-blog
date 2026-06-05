---
title: "leetcode-字典树20"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 与数组中元素的最大异或值


给你一个由非负整数组成的数组 nums 。另有一个查询数组 queries ，其中 queries[i] = [xi, mi] 。

第 i 个查询的答案是 xi 和任何 nums 数组中不超过 mi 的元素按位异或（XOR）得到的最大值。换句话说，答案是 max(nums[j] XOR xi) ，其中所有 j 均满足 nums[j] <= mi 。如果 nums 中的所有元素都大于 mi，最终答案就是 -1 。

返回一个整数数组 answer 作为查询的答案，其中 answer.length == queries.length 且 answer[i] 是第 i 个查询的答案。



```
impl Solution {
    pub fn maximize_xor(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        // 将 nums 转为可变，因为后面需要排序
        let mut nums = nums;
        // 不稳定排序（性能更好），升序排列
        // 排序后可以按顺序插入数字到 Trie，确保插入的数字满足 mi 的限制
        nums.sort_unstable();

        // 构建按位存储的Trie，使用 usize 存储索引，i32 存储最小值
        // 每个节点: [child0, child1, min_val]
        // - child0: 当前位为 0 的子节点索引（0 表示不存在）
        // - child1: 当前位为 1 的子节点索引（0 表示不存在）
        // - min_val: 该节点子树中的最小值（用于剪枝）
        let mut trie: Vec<[usize; 3]> = vec![[0, 0, i32::MAX as usize]];  // 根节点
        // 预分配内存，避免插入过程中频繁重新分配（提高性能）
        trie.reserve(nums.len() * 32);

        // 遍历所有数字，插入到 Trie 中
        for &num in &nums {
            let mut node = 0;  // 从根节点开始（根节点索引为 0）

            // 从最高位（第31位）到最低位（第0位）处理
            // 共32位，因为 i32 是32位整数
            for shift in (0..32).rev() {
                // 获取当前位的值（0 或 1）
                // 例如：num = 5 (101)，shift=2 时得到 1
                let bit = ((num >> shift) & 1) as usize;

                // 如果子节点不存在，创建新节点
                if trie[node][bit] == 0 {
                    // 添加新节点：初始子节点为0，最小值为最大值
                    trie.push([0, 0, i32::MAX as usize]);
                    // 将当前节点的子节点指向新创建的节点
                    trie[node][bit] = trie.len() - 1;
                }

                // 移动到子节点
                node = trie[node][bit];

                // 更新当前节点的最小值
                let num_usize = num as usize;  // 转换为 usize 以便存储
                // 如果当前数字比节点记录的最小值还小，则更新
                if num_usize < trie[node][2] {
                    trie[node][2] = num_usize;
                }
            }
        }

        // 自底向上传播最小值
        // 从最后一个节点往前遍历，确保子节点先处理
        for node in (0..trie.len()).rev() {
            let left = trie[node][0];   // 获取左子节点（位为0）
            let right = trie[node][1];  // 获取右子节点（位为1）

            // 如果左子节点存在，且左子树的最小值小于当前节点的最小值
            if left != 0 && trie[left][2] < trie[node][2] {
                trie[node][2] = trie[left][2];  // 更新当前节点最小值
            }
            // 如果右子节点存在，且右子树的最小值小于当前节点的最小值
            if right != 0 && trie[right][2] < trie[node][2] {
                trie[node][2] = trie[right][2];
            }
        }

        // 快速查询函数（闭包）
        // 参数：x - 要异或的数字，m - 数字的上限
        // 返回：与 x 异或的最大值
        let query = |x: i32, m: i32| -> i32 {
            // 剪枝优化：如果根节点的最小值都大于 m
            // 说明所有数字都 > m，没有满足条件的数字
            if (trie[0][2] as i32) > m {
                return -1;  // 返回 -1 表示无解
            }

            let mut node = 0;  // 当前节点索引，从根开始
            let mut ans = 0;   // 最终结果（最大异或值）

            // 从高位到低位遍历（贪心算法）
            for shift in (0..32).rev() {
                // 获取 x 当前位的值（0 或 1）
                let bit = ((x >> shift) & 1) as usize;
                // 理想情况下，我们希望当前位是相反的（这样才能使结果更大）
                // 例如：bit=0 时希望取 1，bit=1 时希望取 0
                let prefer = bit ^ 1;  // 异或运算实现取反

                // 获取首选子节点的索引
                let prefer_child = trie[node][prefer];

                // 检查首选路径是否有效：
                // 1. 子节点存在（prefer_child != 0）
                // 2. 该子树中的最小值 <= m（说明有满足条件的数字）
                if prefer_child != 0 && (trie[prefer_child][2] as i32) <= m {
                    // 可以走首选路径
                    // 因为异或后当前位结果为 1，所以将结果对应的位设为 1
                    ans |= 1 << shift;
                    node = prefer_child;  // 移动到子节点
                } else {
                    // 首选路径不可用，只能走备选路径（相同位）
                    node = trie[node][bit];
                    // 此时异或结果为 0，结果当前位保持 0
                }
            }
            ans  // 返回最大异或值
        };

        // 处理所有查询
        queries
            .into_iter()           // 转换为迭代器
            .map(|q| query(q[0], q[1]))  // 对每个查询执行查询函数
            .collect()             // 收集结果到 Vec<i32>
    }
}
```
