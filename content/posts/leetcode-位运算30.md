---
title: "leetcode-位运算30"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 得到目标数组的最少函数调用次数

```
func modify(arr, op, idx) {
    // add by 1 index index
    if (op == 0) {
        arr[idx] = arr[idx] + 1
    }
    // multiply by 2 all elements
    if (op == 1) {
        for (i = 0; i < arr.length; i++) {
            arr[i] = arr[i] * 2
        }
    }
}
```

给你一个与 nums 大小相同且初始值全为 0 的数组 arr ，请你调用以上函数得到整数数组 nums 。

请你返回将 arr 变成 nums 的最少函数调用次数。

答案保证在 32 位有符号整数以内。

```
impl Solution {
    /// 返回将全 0 数组变为 nums 所需的最少函数调用次数。
    ///
    /// 逆向思考：从 nums 变回全 0。
    /// - 若某元素为奇数，必须先执行一次减 1（对应一次 op=0 调用）；
    /// - 所有元素都变为偶数后，执行一次整体除以 2（对应一次 op=1 调用）。
    ///
    /// 因此总次数 = 所有元素二进制中 1 的个数总和
    ///            + 所有元素变为 0 前所需的“整体除以 2”次数。
    pub fn min_operations(nums: Vec<i32>) -> i32 {
        let mut ops = 0;      // 减 1 操作次数（即所有元素二进制 1 的个数）
        let mut max_bits = 0; // 最大元素所需右移次数

        for &x in &nums {
            ops += x.count_ones() as i32;
            if x > 0 {
                max_bits = max_bits.max(32 - x.leading_zeros());
            }
        }

        // 每个元素右移到 0 需要 (位宽 - 1) 次；整体除 2 需要最大位宽 - 1 次
        ops + (max_bits as i32 - 1).max(0)
    }
}
```
