---
title: "leetcode-双指针10"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 删除有序数组中的重复项 II

给你一个有序数组 nums ，请你 原地 删除重复出现的元素，使得出现次数超过两次的元素只出现两次 ，返回删除后数组的新长度。

不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。



说明：

为什么返回数值是整数，但输出的答案是数组呢？

请注意，输入数组是以「引用」方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

```
  // nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
  int len = removeDuplicates(nums);

  // 在函数里修改输入数组对于调用者是可见的。
  // 根据你的函数返回的长度, 它会打印出数组中 该长度范围内 的所有元素。
  for (int i = 0; i < len; i++) {
      print(nums[i]);
  }
```


```
impl Solution {
    /// 原地删除重复元素，使每个元素最多出现两次（双指针法）
    /// 时间复杂度 O(n)，空间复杂度 O(1)
    /// slow 指向下一个可写入位置，fast 遍历数组
    pub fn remove_duplicates(nums: &mut Vec<i32>) -> i32 {
        let n = nums.len();
        if n <= 2 {
            return n as i32;
        }

        let mut slow = 2;
        for fast in 2..n {
            // 当前元素与前两个元素不同时，才保留
            if nums[fast] != nums[slow - 2] {
                nums[slow] = nums[fast];
                slow += 1;
            }
        }

        slow as i32
    }
}
```
