---
title: "leetcode-单调栈5"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 拼接最大数

给你两个整数数组 nums1 和 nums2，它们的长度分别为 m 和 n。数组 nums1 和 nums2 分别代表两个数各位上的数字。同时你也会得到一个整数 k。

请你利用这两个数组中的数字创建一个长度为 k <= m + n 的最大数。同一数组中数字的相对顺序必须保持不变。

返回代表答案的长度为 k 的数组。



```
impl Solution {
    pub fn max_number(nums1: Vec<i32>, nums2: Vec<i32>, k: i32) -> Vec<i32> {
        let (m, n) = (nums1.len(), nums2.len());
        // 从 nums1 中取 x 个，从 nums2 中取 k-x 个
        // x 的取值范围：确保两个数组都能提供足够的元素
        ((k - n as i32).max(0)..k.min(m as i32) + 1)
            .map(|x| Self::merge(Self::select_max(&nums1, x), Self::select_max(&nums2, k - x)))
            .max()  // 比较所有组合结果，取最大的数
            .unwrap()
    }

    // 合并两个数组，按字典序保持最大
    fn merge(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
        let mut ans = vec![];
        let (mut i, mut j) = (0, 0);
        while i < nums1.len() && j < nums2.len() {
            // 比较剩余部分字典序，谁大就先取谁
            if nums1[i..] > nums2[j..] {
                ans.push(nums1[i]);
                i += 1;
            } else {
                ans.push(nums2[j]);
                j += 1;
            }
        }
        ans.extend(&nums1[i..]);  // 添加剩余部分
        ans.extend(&nums2[j..]);
        ans
    }

    // 从数组中选出长度为 k 的最大子序列（保持原顺序）
    fn select_max(nums: &[i32], k: i32) -> Vec<i32> {
        let mut to_drop = nums.len() - k as usize;  // 需要丢弃的元素个数
        let mut stk = vec![i32::MAX];  // 单调递减栈，初始化哨兵

        for num in nums {
            // 当还有丢弃名额，且栈顶元素小于当前元素时，弹出栈顶（丢弃它）
            while to_drop > 0 && stk.last() < Some(num) {
                stk.pop();
                to_drop -= 1;
            }
            stk.push(*num);
        }

        // 如果还需要丢弃，从末尾丢弃
        stk[1..stk.len() - to_drop].to_owned()
    }
}
```
