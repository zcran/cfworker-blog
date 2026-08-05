---
title: "leetcode-计数47"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 从子集的和还原数组

存在一个未知数组需要你进行还原，给你一个整数 n 表示该数组的长度。另给你一个数组 sums ，由未知数组中全部 2n 个 子集的和 组成（子集中的元素没有特定的顺序）。

返回一个长度为 n 的数组 ans 表示还原得到的未知数组。如果存在 多种 答案，只需返回其中 任意一个 。

如果可以由数组 arr 删除部分元素（也可能不删除或全删除）得到数组 sub ，那么数组 sub 就是数组 arr 的一个 子集 。sub 的元素之和就是 arr 的一个 子集的和 。一个空数组的元素之和为 0 。

注意：生成的测试用例将保证至少存在一个正确答案。


```
impl Solution {
    pub fn recover_array(n: i32, mut sums: Vec<i32>) -> Vec<i32> {
        // 存储最终还原的数组
        let mut result = Vec::new();
        // 对子集和数组排序，便于差分和二分查找
        sums.sort_unstable();

        // 每次迭代还原一个数字，共还原 n 个
        for _ in 0..n {
            // 候选差值：排序后前两个元素的差
            let diff = sums[1] - sums[0];
            let half = sums.len() / 2;

            // 两个分组：left 和 right，各占一半
            let mut left_group = vec![0; half];
            let mut right_group = vec![0; half];

            // 从大到小配对：x 与 x - diff 配对
            for i in (0..half).rev() {
                // 复制当前最大值，避免借用冲突
                let max_val = *sums.last().unwrap();
                // 其配对值应为 max_val - diff
                let pair_val = max_val - diff;

                // 移除当前最大值
                sums.pop();
                // 查找并移除配对值
                let pos = sums.binary_search(&pair_val).unwrap();
                sums.remove(pos);

                // 将配对值存入 left_group，最大值存入 right_group
                // 倒序存储保持与原始顺序一致
                left_group[i] = pair_val;
                right_group[i] = max_val;
            }

            // 判断 left_group 是否包含 0
            // 如果包含 0，说明原数组包含 diff，否则包含 -diff
            let contains_zero = left_group.contains(&0);
            result.push(if contains_zero { diff } else { -diff });

            // 选择包含 0 的分组继续下一轮
            sums = if contains_zero { left_group } else { right_group };
        }

        result
    }
}
```
