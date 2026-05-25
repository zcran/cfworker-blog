---
title: "leetcode-位掩码6"
date: 2026-05-18T10:27:58+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 平方数组的数目

如果一个数组的任意两个相邻元素之和都是 完全平方数 ，则该数组称为 平方数组 。

给定一个整数数组 nums，返回所有属于 平方数组 的 nums 的排列数量。

如果存在某个索引 i 使得 perm1[i] != perm2[i]，则认为两个排列 perm1 和 perm2 不同。

```
impl Solution {
    pub fn num_squareful_perms(mut nums: Vec<i32>) -> i32 {
        // 排序以便于剪枝重复元素
        nums.sort_unstable();
        let n = nums.len();
        let mut used = vec![false; n];
        let mut count = 0;

        // 辅助函数：判断两数之和是否为完全平方数（整数平方根）
        fn is_squareful(a: i32, b: i32) -> bool {
            let s = a + b;
            let root = (s as f64).sqrt() as i32;
            root * root == s
        }

        // 回溯生成排列
        fn backtrack(
            nums: &[i32],
            used: &mut [bool],
            prev: Option<i32>,
            depth: usize,
            count: &mut i32,
        ) {
            if depth == nums.len() {
                *count += 1;
                return;
            }
            for i in 0..nums.len() {
                // 剪枝1：当前元素已使用
                if used[i] { continue; }
                // 剪枝2：跳过重复值（前一个相同值且未使用）
                if i > 0 && nums[i] == nums[i - 1] && !used[i - 1] { continue; }
                // 剪枝3：检查与前一个元素是否构成平方数
                if let Some(p) = prev {
                    if !is_squareful(p, nums[i]) { continue; }
                }
                used[i] = true;
                backtrack(nums, used, Some(nums[i]), depth + 1, count);
                used[i] = false;
            }
        }

        backtrack(&nums, &mut used, None, 0, &mut count);
        count
    }
}
```
