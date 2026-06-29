---
title: "leetcode-单调栈36"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 使数组按非递减顺序排列

给你一个下标从 0 开始的整数数组 nums 。在一步操作中，移除所有满足 nums[i - 1] > nums[i] 的 nums[i] ，其中 0 < i < nums.length 。

重复执行步骤，直到 nums 变为 非递减 数组，返回所需执行的操作数。

```
impl Solution {
    /// 计算删除所有元素所需的总步数
    /// 规则：每一轮删除所有比右侧相邻元素小的元素
    pub fn total_steps(nums: Vec<i32>) -> i32 {
        let (mut count, mut ret) = (0, 0);  // count: 当前元素被删除的轮次, ret: 全局最大轮次
        let mut st: Vec<(i32, i32)> = vec![];  // 单调递减栈，存储(元素值, 该元素被删除的轮次)

        nums.iter().for_each(|&x| {  // 遍历每个元素
            count = 0;  // 重置当前元素被删除的轮次

            // 维护单调递减栈：当前元素 >= 栈顶元素时，栈顶元素会被删除
            while !st.is_empty() && x >= st[st.len() - 1].0 {
                // 当前元素会"杀死"栈顶元素，取两者中被删除轮次的最大值
                count = count.max(st.pop().unwrap().1);
            }

            // 如果栈为空，说明当前元素左侧没有比它大的元素，不会被删除（轮次为0）
            // 否则，当前元素会在栈顶元素被删除的下一轮被删除（轮次+1）
            count = if st.is_empty() { 0 } else { count + 1 };

            ret = ret.max(count);  // 更新全局最大轮次
            st.push((x, count));   // 将当前元素及其被删除轮次入栈
        });

        ret  // 返回总步数
    }
}
```
