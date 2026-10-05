---
title: "leetcode-双指针62"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 复写零

给你一个长度固定的整数数组 arr ，请你将该数组中出现的每个零都复写一遍，并将其余的元素向右平移。

注意：请不要在超过该数组长度的位置写入元素。请对输入的数组 就地 进行上述修改，不要从函数返回任何东西。


```
impl Solution {
    pub fn duplicate_zeros(arr: &mut Vec<i32>) {
        let n = arr.len();
        // 统计所有零的个数（包括可能超出数组长度的部分）
        let zeros = arr.iter().filter(|&&x| x == 0).count();

        // j 指向最终数组的末尾（允许超出原数组范围，超出部分将被丢弃）
        let mut j = (n - 1 + zeros) as i32;

        // 从后向前遍历原数组
        for i in (0..n).rev() {
            // 只有当 j 在有效范围内时才写入
            if j >= 0 && j < n as i32 {
                arr[j as usize] = arr[i];
            }

            // 如果当前元素是零，需要额外复制一个零
            if arr[i] == 0 {
                j -= 1;
                if j >= 0 && j < n as i32 {
                    arr[j as usize] = 0;
                }
            }

            // 已放置一个元素，j 继续前移
            j -= 1;
        }
    }
}
```
