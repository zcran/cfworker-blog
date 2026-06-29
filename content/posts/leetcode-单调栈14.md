---
title: "leetcode-单调栈14"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 最多能完成排序的块

给定一个长度为 n 的整数数组 arr ，它表示在 [0, n - 1] 范围内的整数的排列。

我们将 arr 分割成若干 块 (即分区)，并对每个块单独排序。将它们连接起来后，使得连接的结果和按升序排序后的原数组相同。

返回数组能分成的最多块数量。

```
impl Solution {
    pub fn max_chunks_to_sorted(arr: Vec<i32>) -> i32 {
        let n = arr.len();
        let (mut diff, mut ans) = (0, 0);
        for i in 0..n {
            diff += (arr[i] - i as i32);
            if (diff == 0) {
                ans += 1;
            }
        }
        return ans;
    }
}
```
