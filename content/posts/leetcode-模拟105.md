---
title: "leetcode-模拟105"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 将元素分配到两个数组中 I

给你一个下标从 1 开始、包含 不同 整数的数组 nums ，数组长度为 n 。

你需要通过 n 次操作，将 nums 中的所有元素分配到两个数组 arr1 和 arr2 中。在第一次操作中，将 nums[1] 追加到 arr1 。在第二次操作中，将 nums[2] 追加到 arr2 。之后，在第 i 次操作中：

如果 arr1 的最后一个元素 大于 arr2 的最后一个元素，就将 nums[i] 追加到 arr1 。否则，将 nums[i] 追加到 arr2 。

通过连接数组 arr1 和 arr2 形成数组 result 。例如，如果 arr1 == [1,2,3] 且 arr2 == [4,5,6] ，那么 result = [1,2,3,4,5,6] 。

返回数组 result 。


```
impl Solution {
    pub fn result_array(nums: Vec<i32>) -> Vec<i32> {
        let mut arr1 = vec![nums[0]];
        let mut arr2 = vec![nums[1]];

        // 跟踪末尾元素，避免每次 last().unwrap() 的开销
        let (mut last1, mut last2) = (nums[0], nums[1]);

        for &num in &nums[2..] {
            if last1 > last2 {
                arr1.push(num);
                last1 = num;
            } else {
                arr2.push(num);
                last2 = num;
            }
        }

        arr1.extend(arr2);
        arr1
    }
}
```
