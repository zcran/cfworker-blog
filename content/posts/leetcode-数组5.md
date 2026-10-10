---
title: "leetcode-数组5"
date: 2026-10-10T20:38:40+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 数组嵌套

索引从0开始长度为N的数组A，包含0到N - 1的所有整数。找到最大的集合S并返回其大小，其中 S[i] = {A[i], A[A[i]], A[A[A[i]]], ... }且遵守以下的规则。

假设选择索引为i的元素A[i]为S的第一个元素，S的下一个元素应该是A[A[i]]，之后是A[A[A[i]]]... 以此类推，不断添加直到S出现重复的元素。


```
impl Solution {
    /// 求数组嵌套的最大集合大小。
    ///
    /// 每个索引指向下一个索引，形成若干条有向环路径。
    /// 遍历每个未访问的起点，沿路径前进并标记已访问（置 -1），
    /// 统计当前路径长度，取所有路径长度的最大值。
    ///
    /// 时间复杂度 O(n)，每个元素最多访问一次；
    /// 空间复杂度 O(1)，原地标记，无额外分配。
    pub fn array_nesting(mut nums: Vec<i32>) -> i32 {
        let mut max_len = 0;

        for i in 0..nums.len() {
            if nums[i] == -1 {
                continue;
            }

            let mut cnt = 0;
            let mut index = i;

            // 沿路径前进，直到遇到已访问节点
            while nums[index] != -1 {
                let next = nums[index] as usize;
                nums[index] = -1; // 标记已访问
                index = next;
                cnt += 1;
            }

            max_len = max_len.max(cnt);
        }

        max_len
    }
}
```
