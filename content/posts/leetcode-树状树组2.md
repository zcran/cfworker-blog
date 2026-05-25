---
title: "leetcode-树状树组2"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 区域和检索 - 数组可修改

给你一个数组 nums ，请你完成两类查询。

1.其中一类查询要求 更新 数组 nums 下标对应的值
2.另一类查询要求返回数组 nums 中索引 left 和索引 right 之间（ 包含 ）的nums元素的 和 ，其中 left <= right

实现 NumArray 类：

·NumArray(int[] nums) 用整数数组 nums 初始化对象
·void update(int index, int val) 将 nums[index] 的值 更新 为 val
·int sumRange(int left, int right) 返回数组 nums 中索引 left 和索引 right 之间（ 包含 ）的nums元素的 和 （即，nums[left] + nums[left + 1], ..., nums[right]）

```
/// 线段树（非递归，完全二叉树数组表示）
/// 支持单点更新与区间求和，时间复杂度 O(log n)
struct NumArray {
    tree: Vec<i32>, // 线段树数组，长度 = 2 * n
    n: usize,       // 原始数组长度
}

impl NumArray {
    /// 构建线段树
    /// 使用函数式风格初始化叶子节点，再自底向上构建内部节点
    fn new(nums: Vec<i32>) -> Self {
        let n = nums.len();
        // 叶子节点存放在 tree[n..2n)，初始为 0；并用 nums 填充
        let mut tree = (0..2 * n)
            .map(|i| {
                if i >= n {
                    nums[i - n] // 叶子节点
                } else {
                    0 // 内部节点暂为 0，后续填充
                }
            })
            .collect::<Vec<_>>();

        // 自底向上构建内部节点：每个父节点 = 左子 + 右子
        for i in (1..n).rev() {
            tree[i] = tree[i << 1] + tree[i << 1 | 1];
        }

        NumArray { tree, n }
    }

    /// 更新下标 index 的值为 val（0‑based）
    fn update(&mut self, index: i32, val: i32) {
        let index = index as usize + self.n; // 叶子实际位置
        self.tree[index] = val;

        // 向上更新所有祖先节点
        let mut i = index;
        while i > 1 {
            i >>= 1; // 父节点下标
            self.tree[i] = self.tree[i << 1] + self.tree[i << 1 | 1];
        }
    }

    /// 计算闭区间 [left, right] 的元素和（0‑based）
    fn sum_range(&self, left: i32, right: i32) -> i32 {
        let (mut l, mut r) = (left as usize + self.n, right as usize + self.n);
        let mut sum = 0;

        while l <= r {
            // 如果 l 是右子节点，则将其值加入并右移
            if l & 1 == 1 {
                sum += self.tree[l];
                l += 1;
            }
            // 如果 r 是左子节点，则将其值加入并左移
            if r & 1 == 0 {
                sum += self.tree[r];
                r -= 1;
            }
            l >>= 1;
            r >>= 1;
        }
        sum
    }
}

/*
 * 使用示例：
 * let obj = NumArray::new(nums);
 * obj.update(index, val);
 * let result = obj.sum_range(left, right);
 */
```
