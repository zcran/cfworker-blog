---
title: "leetcode-模拟78"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 对数组执行操作

给你一个下标从 0 开始的数组 nums ，数组大小为 n ，且由 非负 整数组成。

你需要对数组执行 n - 1 步操作，其中第 i 步操作（从 0 开始计数）要求对 nums 中第 i 个元素执行下述指令：

· 如果 nums[i] == nums[i + 1] ，则 nums[i] 的值变成原来的 2 倍，nums[i + 1] 的值变成 0 。否则，跳过这步操作。

在执行完 全部 操作后，将所有 0 移动 到数组的 末尾 。

· 例如，数组 [1,0,2,0,0,1] 将所有 0 移动到末尾后变为 [1,2,1,0,0,0] 。

返回结果数组。

注意 操作应当 依次有序 执行，而不是一次性全部执行。


```
impl Solution {
    /// 先原地执行 n-1 步合并操作，再用双指针 swap 将非零元素压缩到前面。
    /// 时间 O(n)，额外空间 O(1)。
    pub fn apply_operations(mut nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();

        // 依次执行合并：相邻相等则翻倍当前、下一个置零
        for i in 0..n - 1 {
            if nums[i] == nums[i + 1] {
                nums[i] <<= 1;
                nums[i + 1] = 0;
            }
        }

        // 双指针：把非零元素 swap 到数组前面
        let mut j = 0;
        for i in 0..n {
            if nums[i] != 0 {
                nums.swap(i, j);
                j += 1;
            }
        }

        nums
    }
}
```
