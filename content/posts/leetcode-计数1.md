---
title: "leetcode-计数1"
date: 2026-08-01T10:40:50+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 多数元素 II

给定一个大小为 n 的整数数组，找出其中所有出现超过 ⌊n / 3⌋ 次的元素。


```
impl Solution {
    /// 找出数组中所有出现超过 ⌊n/3⌋ 次的元素
    ///
    /// # 算法原理
    /// 使用 Boyer-Moore 多数投票算法的扩展版本（摩尔投票法）
    /// 因为最多只有 2 个元素能满足出现次数 > n/3，所以维护两个候选者
    ///
    /// # 算法步骤
    /// 1. **投票阶段**：遍历数组，维护两个候选元素及其票数
    ///    - 如果当前元素等于候选1，候选1票数+1
    ///    - 否则如果当前元素等于候选2，候选2票数+1
    ///    - 否则如果候选1票数为0，将当前元素设为候选1，票数设为1
    ///    - 否则如果候选2票数为0，将当前元素设为候选2，票数设为1
    ///    - 否则两个候选票数都减1
    /// 2. **验证阶段**：统计两个候选元素在数组中的实际出现次数，判断是否 > n/3
    ///
    /// # 复杂度
    /// - 时间：O(n)，遍历数组两遍
    /// - 空间：O(1)，仅使用常量额外空间
    pub fn majority_element(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        if n == 0 {
            return Vec::new();
        }

        // 候选元素及其票数
        let (mut candidate1, mut candidate2) = (0, 0);
        let (mut count1, mut count2) = (0, 0);

        // 第一遍：投票阶段，找出两个可能的候选者
        for &num in &nums {
            if count1 > 0 && num == candidate1 {
                count1 += 1;
            } else if count2 > 0 && num == candidate2 {
                count2 += 1;
            } else if count1 == 0 {
                candidate1 = num;
                count1 = 1;
            } else if count2 == 0 {
                candidate2 = num;
                count2 = 1;
            } else {
                count1 -= 1;
                count2 -= 1;
            }
        }

        // 第二遍：验证候选者是否真正满足条件
        let threshold = n / 3;
        let mut result = Vec::new();

        // 重新统计候选1的出现次数
        if count1 > 0 {
            let freq = nums.iter().filter(|&&x| x == candidate1).count();
            if freq > threshold {
                result.push(candidate1);
            }
        }

        // 重新统计候选2的出现次数（注意去重）
        if count2 > 0 && candidate2 != candidate1 {
            let freq = nums.iter().filter(|&&x| x == candidate2).count();
            if freq > threshold {
                result.push(candidate2);
            }
        }

        result
    }
}
```
