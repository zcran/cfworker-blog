---
title: "leetcode-模拟86"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 找出数组的串联值

给你一个下标从 0 开始的整数数组 nums 。

现定义两个数字的 串联 是由这两个数值串联起来形成的新数字。

· 例如，15 和 49 的串联是 1549 。

nums 的 串联值 最初等于 0 。执行下述操作直到 nums 变为空：

· 如果 nums 的长度大于 1，分别选中 nums 中的第一个元素和最后一个元素，将二者串联得到的值加到 nums 的 串联值 上，然后从 nums 中删除第一个和最后一个元素。例如，如果 nums 是 [1, 2, 4, 5, 6]，将 16 添加到串联值。
· 如果 nums 中仅存在一个元素，则将该元素的值加到 nums 的串联值上，然后删除这个元素。

返回执行完所有操作后 nums 的串联值。


```
impl Solution {
    pub fn find_the_array_conc_val(nums: Vec<i32>) -> i64 {
        let mut ans = 0i64;
        let mut i = 0;
        let mut j = nums.len().saturating_sub(1);

        while i < j {
            // 计算右侧数字的位数，左侧左移相应位数后相加
            let right = nums[j] as i64;
            let left = (nums[i] as i64) * 10i64.pow(right.ilog10() + 1);
            ans += left + right;
            i += 1;
            j -= 1;
        }

        // 如果长度为奇数，中间元素直接加入
        if i == j {
            ans += nums[i] as i64;
        }

        ans
    }
}
```
