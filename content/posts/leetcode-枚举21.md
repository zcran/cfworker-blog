---
title: "leetcode-枚举21"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 还原原数组

Alice 有一个下标从 0 开始的数组 arr ，由 n 个正整数组成。她会选择一个任意的 正整数 k 并按下述方式创建两个下标从 0 开始的新整数数组 lower 和 higher ：

1.对每个满足 0 <= i < n 的下标 i ，lower[i] = arr[i] - k
2.对每个满足 0 <= i < n 的下标 i ，higher[i] = arr[i] + k

不幸地是，Alice 丢失了全部三个数组。但是，她记住了在数组 lower 和 higher 中出现的整数，但不知道每个整数属于哪个数组。请你帮助 Alice 还原原数组。

给你一个由 2n 个整数组成的整数数组 nums ，其中 恰好 n 个整数出现在 lower ，剩下的出现在 higher ，还原并返回 原数组 arr 。如果出现答案不唯一的情况，返回 任一 有效数组。

注意：生成的测试用例保证存在 至少一个 有效数组 arr 。


```
impl Solution {
    /// 还原原数组 arr。
    /// 给定 2n 个整数，其中 n 个来自 lower[i] = arr[i] - k，n 个来自 higher[i] = arr[i] + k。
    /// 返回任一有效的 arr。
    pub fn recover_array(nums: Vec<i32>) -> Vec<i32> {
        let mut nums = nums;
        nums.sort_unstable();
        let n = nums.len();
        let half = n / 2;

        // 枚举与 nums[0] 配对的 higher 元素
        // nums[0] 一定是某个 lower 中的最小值，与其配对的 higher = nums[0] + 2k
        for i in 1..n {
            let diff = nums[i] - nums[0];
            // 差值必须为正偶数（2k > 0）
            if diff <= 0 || diff % 2 != 0 {
                continue;
            }

            let k = diff / 2;
            let target_diff = 2 * k;
            let mut used = vec![false; n];
            let mut result = Vec::with_capacity(half);

            // 标记 nums[0] 和 nums[i] 已使用
            used[0] = true;
            used[i] = true;
            result.push(nums[0] + k);

            let mut left = 0;
            let mut right = i;

            // 每次配对 lower = nums[left]，higher = nums[left] + 2k
            for _ in 1..half {
                // 找到下一个未使用的 lower
                while left < n && used[left] {
                    left += 1;
                }

                // 如果 left 超出范围，说明配对失败
                if left >= n {
                    break;
                }

                // 找到匹配的 higher
                while right < n && (used[right] || nums[right] - nums[left] != target_diff) {
                    right += 1;
                }

                // 没有找到匹配，当前 k 无效
                if right >= n {
                    break;
                }

                result.push(nums[left] + k);
                used[left] = true;
                used[right] = true;
            }

            // 如果成功配对所有元素，返回结果
            if result.len() == half {
                return result;
            }
        }

        // 题目保证一定有解，但为了类型安全，返回空 Vec
        // 注意：实际运行中不会执行到这里
        Vec::new()
    }
}
```
