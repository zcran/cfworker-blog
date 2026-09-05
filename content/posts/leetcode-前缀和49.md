---
title: "leetcode-前缀和49"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 移动所有球到每个盒子所需的最小操作数

有 n 个盒子。给你一个长度为 n 的二进制字符串 boxes ，其中 boxes[i] 的值为 '0' 表示第 i 个盒子是 空 的，而 boxes[i] 的值为 '1' 表示盒子里有 一个 小球。

在一步操作中，你可以将 一个 小球从某个盒子移动到一个与之相邻的盒子中。第 i 个盒子和第 j 个盒子相邻需满足 abs(i - j) == 1 。注意，操作执行后，某些盒子中可能会存在不止一个小球。

返回一个长度为 n 的数组 answer ，其中 answer[i] 是将所有小球移动到第 i 个盒子所需的 最小 操作数。

每个 answer[i] 都需要根据盒子的 初始状态 进行计算。


```
impl Solution {
    pub fn min_operations(boxes: String) -> Vec<i32> {
        let bytes = boxes.as_bytes();
        let n = bytes.len();

        // 计算所有小球移动到第0个盒子的操作数
        let mut ops = 0;
        let mut count = 0;
        for (i, &b) in bytes.iter().enumerate() {
            if b == b'1' {
                ops += i as i32;
                count += 1;
            }
        }

        let mut ans = Vec::with_capacity(n);
        let mut left_count = 0; // 当前位置左边的小球数

        for (i, &b) in bytes.iter().enumerate() {
            ans.push(ops);

            // 更新状态：移动到下一个位置
            if b == b'1' {
                left_count += 1;
                count -= 1;
            }
            // 左边的小球远离1步（+left_count），右边的小球靠近1步（-count）
            ops += left_count - count;
        }

        ans
    }
}
```
