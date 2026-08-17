---
title: "leetcode-模拟118"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 使数组元素等于零

给你一个整数数组 nums 。

开始时，选择一个满足 nums[curr] == 0 的起始位置 curr ，并选择一个移动 方向 ：向左或者向右。

此后，你需要重复下面的过程：

· 如果 curr 超过范围 [0, n - 1] ，过程结束。
· 如果 nums[curr] == 0 ，沿当前方向继续移动：如果向右移，则 递增 curr ；如果向左移，则 递减 curr 。
· 如果 nums[curr] > 0:
    · 将 nums[curr] 减 1 。
    · 反转 移动方向（向左变向右，反之亦然）。
    · 沿新方向移动一步。

如果在结束整个过程后，nums 中的所有元素都变为 0 ，则认为选出的初始位置和移动方向 有效 。

返回可能的有效选择方案数目。


```
impl Solution {
    /// 统计所有有效的起始位置和方向组合数
    ///
    /// 规则：从值为 0 的位置出发，选择左/右方向。
    /// - 遇到 0：沿当前方向继续移动
    /// - 遇到正数：减 1，反转方向，沿新方向移动一步
    /// 有效条件：过程结束后所有元素都变为 0
    ///
    /// # 优化思路
    /// - 预处理非零元素个数，避免每次重新计算
    /// - 方向用 i32（-1 / 1）表示，反转时直接乘以 -1
    pub fn count_valid_selections(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let nonzero_count = nums.iter().filter(|&&x| x > 0).count() as i32;
        let mut valid = 0;

        for (idx, &val) in nums.iter().enumerate() {
            if val != 0 {
                continue;
            }
            // 从位置 idx 分别尝试向左(-1)和向右(1)两个方向
            for &dir in &[-1i32, 1i32] {
                if Self::simulate(&nums, nonzero_count, idx as i32, dir) {
                    valid += 1;
                }
            }
        }

        valid
    }

    /// 模拟从指定位置、指定方向出发的整个过程
    ///
    /// # 参数
    /// - `nums`: 原始数组（只读引用）
    /// - `nonzero_count`: 非零元素总数，用于提前终止
    /// - `start`: 起始位置
    /// - `direction`: 初始方向，-1 表示向左，1 表示向右
    ///
    /// # 返回值
    /// - 是否所有元素最终都变为 0
    fn simulate(nums: &[i32], nonzero_count: i32, start: i32, mut direction: i32) -> bool {
        let n = nums.len() as i32;
        let mut arr = nums.to_vec();   // 复制数组用于模拟修改
        let mut remaining = nonzero_count;
        let mut curr = start;

        // 当还有非零元素且未越界时继续模拟
        while remaining > 0 && curr >= 0 && curr < n {
            let idx = curr as usize;
            if arr[idx] > 0 {
                arr[idx] -= 1;
                if arr[idx] == 0 {
                    remaining -= 1;
                }
                direction = -direction;  // 反转方向
            }
            curr += direction;
        }

        remaining == 0
    }
}
```
