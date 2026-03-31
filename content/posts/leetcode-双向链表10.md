---
title: "Leetcode 双向链表10"
date: 2025-06-16T22:16:50+08:00
tags: ["leetcode", "双向链表"]
draft: false
---

## 移除最小数对使数组有序 II

给你一个数组 nums，你可以执行以下操作任意次数：


· 选择 相邻 元素对中 和最小 的一对。如果存在多个这样的对，选择最左边的一个。
· 用它们的和替换这对元素。
返回将数组变为 非递减 所需的 最小操作次数 。

如果一个数组中每个元素都大于或等于它前一个元素（如果存在的话），则称该数组为非递减。



示例 1：

输入： nums = [5,2,3,1]

输出： 2

解释：

元素对 (3,1) 的和最小，为 4。替换后 nums = [5,2,4]。
元素对 (2,4) 的和为 6。替换后 nums = [5,6]。
数组 nums 在两次操作后变为非递减。

示例 2：

输入： nums = [1,2,2]

输出： 0

解释：

数组 nums 已经是非递减的。


```
impl Solution {



pub fn minimum_pair_removal(nums: Vec<i32>) -> i32 {
    use std::cmp::Reverse;
use std::collections::BinaryHeap;

    let n = nums.len();
    if n <= 1 {
        return 0;
    }

    let mut nums: Vec<i64> = nums.into_iter().map(|x| x as i64).collect();

    let mut left = vec![usize::MAX; n];
    let mut right = vec![usize::MAX; n];

    for i in 0..n {
        if i > 0 {
            left[i] = i - 1;
        }
        if i + 1 < n {
            right[i] = i + 1;
        }
    }

    // 最小堆： (相邻和, 右元素下标)
    let mut heap = BinaryHeap::<Reverse<(i64, usize)>>::new();
    for i in 1..n {
        heap.push(Reverse((nums[i - 1] + nums[i], i)));
    }

    // 统计当前「逆序对」数量
    let mut bad = 0;
    for i in 1..n {
        if nums[i] < nums[i - 1] {
            bad += 1;
        }
    }

    let mut res = 0;
    // 数组转链表保障了 加减元素的快捷性。
    // heap 是一个有序的
    // 将计算结果进行排序？并且利用数据结构存储这个排序
    // 如果出现bad，就调整排序？并且计算影响的范围

    while bad > 0 {
        let Reverse((sum, i)) = heap.pop().unwrap();

        let l = left[i];
        if l == usize::MAX {
            continue; // 这个 pair 已经失效
        }

        // 懒删除校验
        if nums[l] + nums[i] != sum {
            continue;
        }

        // ---------- 更新 bad（合并前，先删旧影响） ----------
        if left[l] != usize::MAX && nums[l] < nums[left[l]] {
            bad -= 1;
        }
        if right[i] != usize::MAX && nums[right[i]] < nums[i] {
            bad -= 1;
        }
        if nums[i] < nums[l] {
            bad -= 1;
        }

        // ---------- 合并 ----------
        nums[l] += nums[i];
        res += 1;

        // 从链表中删除 i
        let r = right[i];
        right[l] = r;
        if r != usize::MAX {
            left[r] = l;
        }
        left[i] = usize::MAX;

        // ---------- 更新 bad（合并后，新增影响） ----------
        if left[l] != usize::MAX && nums[l] < nums[left[l]] {
            bad += 1;
        }
        if r != usize::MAX && nums[r] < nums[l] {
            bad += 1;
        }

        // ---------- 更新堆 ----------
        if left[l] != usize::MAX {
            let ll = left[l];
            heap.push(Reverse((nums[ll] + nums[l], l)));
        }
        if r != usize::MAX {
            heap.push(Reverse((nums[l] + nums[r], r)));
        }
    }

    res
}
}
```



巧妙设计

1. 懒删除机制

堆中的pair可能因为合并而失效，通过检查 nums[l] + nums[i] != sum 来跳过无效项。

2. 增量更新bad计数

不需要每次重新扫描整个数组，只更新受影响的前后关系，将复杂度从O(n²)降到O(n log n)。

3. 链表索引管理

使用 usize::MAX 表示空节点，避免了动态内存分配。

时间复杂度

每次操作：O(log n)（堆操作）
总操作次数 ≤ n-1
总体：O(n log n)
