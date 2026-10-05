---
title: "leetcode-双指针9"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 颜色分类

给定一个包含红色、白色和蓝色、共 n 个元素的数组 nums ，原地 对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

必须在不使用库内置的 sort 函数的情况下解决这个问题。


```
impl Solution {
    /// 荷兰国旗问题（三色排序）
    /// 时间复杂度 O(n)，空间复杂度 O(1)
    /// 双指针法：p0 指向下一个 0 的位置，p2 指向下一个 2 的位置
    pub fn sort_colors(nums: &mut Vec<i32>) {
        let n = nums.len();
        if n < 2 {
            return;
        }

        let (mut p0, mut p2) = (0, n - 1);
        let mut i = 0;

        while i <= p2 {
            match nums[i] {
                0 => {
                    nums.swap(i, p0);
                    p0 += 1;
                    i += 1;
                }
                2 => {
                    nums.swap(i, p2);
                    // 防止 p2 下溢
                    if p2 == 0 {
                        break;
                    }
                    p2 -= 1;
                    // 注意：交换后，i 位置可能是 0 或 1，需要再次检查，所以不增加 i
                }
                _ => i += 1, // nums[i] == 1
            }
        }
    }
}
```
