---
title: "leetcode-双指针6"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 删除有序数组中的重复项

给你一个 非严格递增排列 的数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。然后返回 nums 中唯一元素的个数。

考虑 nums 的唯一元素的数量为 k。去重后，返回唯一元素的数量 k。

nums 的前 k 个元素应包含 排序后 的唯一数字。下标 k - 1 之后的剩余元素可以忽略。

判题标准:

系统会用下面的代码来测试你的题解:

```
  int[] nums = [...]; // 输入数组
  int[] expectedNums = [...]; // 长度正确的期望答案

  int k = removeDuplicates(nums); // 调用

  assert k == expectedNums.length;
  for (int i = 0; i < k; i++) {
      assert nums[i] == expectedNums[i];
  }
```
如果所有断言都通过，那么您的题解将被 通过。


```
impl Solution {
    /// 原地删除有序数组中的重复项（双指针法）
    /// 时间复杂度 O(n)，空间复杂度 O(1)
    /// 快指针遍历数组，慢指针记录下一个不重复元素的位置
    pub fn remove_duplicates(nums: &mut Vec<i32>) -> i32 {
        let mut k = 0;

        for i in 0..nums.len() {
            // 当前元素是第一个元素，或与上一个元素不同
            if i == 0 || nums[i] != nums[i - 1] {
                nums[k] = nums[i];
                k += 1;
            }
        }

        k as i32
    }
}
```
