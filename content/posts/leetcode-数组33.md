---
title: "leetcode-数组33"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 分组得分最高的所有下标

给你一个下标从 0 开始的二进制数组 nums ，数组长度为 n 。nums 可以按下标 i（ 0 <= i <= n ）拆分成两个数组（可能为空）：numsˇleft 和 numsˇright 。

· numsˇleft 包含 nums 中从下标 0 到 i - 1 的所有元素（包括 0 和 i - 1 ），而 numsˇright 包含 nums 中从下标 i 到 n - 1 的所有元素（包括 i 和 n - 1 ）。
· 如果 i == 0 ，numsˇleft 为 空 ，而 numsˇright 将包含 nums 中的所有元素。
· 如果 i == n ，numsˇleft 将包含 nums 中的所有元素，而 numsˇright 为 空 。

下标 i 的 分组得分 为 numsˇleft 中 0 的个数和 numsˇright 中 1 的个数之 和 。

返回 分组得分 最高 的 所有不同下标 。你可以按 任意顺序 返回答案。


```
impl Solution {
    /// 求分组得分最高的所有不同下标。
    ///
    /// 分组得分 = 左半部分 0 的个数 + 右半部分 1 的个数。
    /// 初始 i = 0 时，左空右全，得分为整个数组 1 的个数。
    /// 从左到右扫描，每遇到一个元素就把下标 i+1 作为新分组点：
    /// - `nums[i] == 0`：左半 0 增 1，得分 +1；
    /// - `nums[i] == 1`：右半 1 减 1，得分 -1。
    /// 扫描过程中维护最高得分及对应下标列表。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)（结果数组）。
    pub fn max_score_indices(nums: Vec<i32>) -> Vec<i32> {
        let mut ones = nums.iter().filter(|&&x| x == 1).count() as i32;
        let mut zeros = 0;
        let mut max_score = ones;
        let mut result = vec![0];

        for (i, &x) in nums.iter().enumerate() {
            if x == 0 {
                zeros += 1;
            } else {
                ones -= 1;
            }

            let score = zeros + ones;
            if score > max_score {
                max_score = score;
                result.clear();
                result.push(i as i32 + 1);
            } else if score == max_score {
                result.push(i as i32 + 1);
            }
        }

        result
    }
}
```
