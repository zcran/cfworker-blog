---
title: "leetcode-单调栈53"
date: 2026-06-07T10:14:06+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 部分排序

给定一个整数数组，编写一个函数，找出索引m和n，只要将索引区间[m,n]的元素排好序，整个数组就是有序的。注意：n-m尽量最小，也就是说，找出符合条件的最短序列。函数返回值为[m,n]，若不存在这样的m和n（例如整个数组是有序的），请返回[-1,-1]。


```
impl Solution {
    /// 找到需要排序的最短子数组的左右边界
    ///
    /// 返回 `[left, right]`，其中：
    /// - left: 需要排序的最左边索引（不存在时返回 -1）
    /// - right: 需要排序的最右边索引（不存在时返回 -1）
    ///
    /// # 算法思路
    /// 使用单调递增栈找到所有破坏单调性的元素，
    /// 最左边的破坏点即为 left，最右边的破坏点即为 right
    pub fn sub_sort(array: Vec<i32>) -> Vec<i32> {
        let n = array.len();
        let mut stack = Vec::with_capacity(n);
        let mut left = -1;
        let mut right = -1;
        let mut max_invalid = i32::MIN;  // 记录所有破坏单调性的元素中的最大值

        for i in 0..n {
            let curr = array[i];

            // 维护单调递增栈：弹出所有大于当前元素的栈顶
            while let Some(&top_idx) = stack.last() {
                if array[top_idx] > curr {
                    // 当前元素破坏了单调性，更新左边界
                    left = if left == -1 {
                        top_idx as i32
                    } else {
                        left.min(top_idx as i32)
                    };
                    // 记录被弹出的元素值，用于后续判断右侧边界
                    max_invalid = max_invalid.max(array[top_idx]);
                    stack.pop();
                } else {
                    break;
                }
            }

            // 如果当前元素小于之前某个被弹出的元素，则它也是无序的
            if curr < max_invalid {
                right = i as i32;
            }

            stack.push(i);
        }

        vec![left, right]
    }
}
```
