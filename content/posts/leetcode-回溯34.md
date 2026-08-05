---
title: "leetcode-回溯34"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 给定数字能组成的最大时间

给定一个由 4 位数字组成的数组，返回可以设置的符合 24 小时制的最大时间。

24 小时格式为 "HH:MM" ，其中 HH 在 00 到 23 之间，MM 在 00 到 59 之间。最小的 24 小时制时间是 00:00 ，而最大的是 23:59 。从 00:00 （午夜）开始算起，过得越久，时间越大。

以长度为 5 的字符串，按 "HH:MM" 格式返回答案。如果不能确定有效时间，则返回空字符串。




```
impl Solution {
    /// 返回由 4 位数字组成的最大有效时间 (HH:MM)
    ///
    /// 思路：回溯枚举所有排列，筛选有效时间并取最大值
    pub fn largest_time_from_digits(mut arr: Vec<i32>) -> String {
        let mut max_time = -1;
        let mut result = String::new();

        fn backtrack(
            arr: &mut [i32],
            pos: usize,
            max_time: &mut i32,
            result: &mut String,
        ) {
            // 已排列完 4 位数字
            if pos == 4 {
                let hour = arr[0] * 10 + arr[1];
                let minute = arr[2] * 10 + arr[3];

                // 检查是否为有效时间
                if hour < 24 && minute < 60 {
                    let time = hour * 60 + minute; // 转换为分钟数便于比较
                    if time > *max_time {
                        *max_time = time;
                        *result = format!("{:02}:{:02}", hour, minute);
                    }
                }
                return;
            }

            // 剪枝优化：首位不能超过 2
            if pos == 0 {
                // 第一位数字（十位小时）只能是 0, 1, 2
                for i in pos..arr.len() {
                    if arr[i] <= 2 {
                        arr.swap(i, pos);
                        backtrack(arr, pos + 1, max_time, result);
                        arr.swap(i, pos);
                    }
                }
                return;
            }

            // 剪枝优化：第二位受第一位限制
            if pos == 1 {
                let first = arr[0];
                // 第一位是 2 时，第二位只能是 0-3
                // 第一位是 0 或 1 时，第二位可以是 0-9
                for i in pos..arr.len() {
                    if first == 2 && arr[i] <= 3 || first < 2 {
                        arr.swap(i, pos);
                        backtrack(arr, pos + 1, max_time, result);
                        arr.swap(i, pos);
                    }
                }
                return;
            }

            // 第三位（分钟十位）只能是 0-5
            if pos == 2 {
                for i in pos..arr.len() {
                    if arr[i] <= 5 {
                        arr.swap(i, pos);
                        backtrack(arr, pos + 1, max_time, result);
                        arr.swap(i, pos);
                    }
                }
                return;
            }

            // 第四位（分钟个位）没有限制
            for i in pos..arr.len() {
                arr.swap(i, pos);
                backtrack(arr, pos + 1, max_time, result);
                arr.swap(i, pos);
            }
        }

        backtrack(&mut arr, 0, &mut max_time, &mut result);
        result
    }
}
```
