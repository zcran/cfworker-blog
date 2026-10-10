---
title: "leetcode-数学10"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 三等分

给定一个由 0 和 1 组成的数组 arr ，将数组分成  3 个非空的部分 ，使得所有这些部分表示相同的二进制值。

如果可以做到，请返回任何 [i, j]，其中 i+1 < j，这样一来：

arr[0], arr[1], ..., arr[i] 为第一部分；
arr[i + 1], arr[i + 2], ..., arr[j - 1] 为第二部分；
arr[j], arr[j + 1], ..., arr[arr.length - 1] 为第三部分。
这三个部分所表示的二进制值相等。

如果无法做到，就返回 [-1, -1]。

注意，在考虑每个部分所表示的二进制时，应当将其看作一个整体。例如，[1,1,0] 表示十进制中的 6，而不会是 3。此外，前导零也是被允许的，所以 [0,1,1] 和 [1,1] 表示相同的值。


```
impl Solution {
    /// 将数组分成三个非空部分，使三部分表示的二进制值相同，返回分割点 [i, j]。
    ///
    /// 思路：
    /// 1. 统计 1 的总数。若为 0，则三部分全为 0，可分割为 [0, 2]。
    /// 2. 若 1 的总数不能被 3 整除，则无法均分，返回 [-1, -1]。
    /// 3. 每部分应含 cnt = total / 3 个 1。找到每部分第一个 1 的位置 i、j、k，
    ///    这三部分的有效位必须完全相同（长度一致、对应位相等），
    ///    尾部剩余的前导零不影响值，只需保证对齐后逐位相等即可。
    /// 4. 从 i、j、k 同步向后比较，直到 k 越界（即第三部分结束）。
    ///    若中途出现不相等，则返回 [-1, -1]；否则第一部分的末尾即为 i-1，
    ///    第二部分的末尾即为 j-1，分割点返回 [i-1, j]。
    pub fn three_equal_parts(arr: Vec<i32>) -> Vec<i32> {
        let total_ones = arr.iter().filter(|&&x| x == 1).count();
        if total_ones == 0 {
            return vec![0, 2];
        }
        if total_ones % 3 != 0 {
            return vec![-1, -1];
        }
        let part_ones = total_ones / 3;

        // 找到三部分各自第一个 1 的下标
        let mut first = [0usize; 3];
        let mut cnt = 0;
        for (i, &v) in arr.iter().enumerate() {
            if v == 1 {
                if cnt % part_ones == 0 && cnt / part_ones < 3 {
                    first[cnt / part_ones] = i;
                }
                cnt += 1;
            }
        }

        // 从三个起点同步比较，验证三部分二进制值相等
        let (mut i, mut j, mut k) = (first[0], first[1], first[2]);
        while k < arr.len() {
            if arr[i] != arr[j] || arr[j] != arr[k] {
                return vec![-1, -1];
            }
            i += 1;
            j += 1;
            k += 1;
        }

        vec![i as i32 - 1, j as i32]
    }
}
```
