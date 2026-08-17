---
title: "leetcode-模拟17"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 翻转图像

给定一个 n x n 的二进制矩阵 image ，先 水平 翻转图像，然后 反转 图像并返回 结果 。

水平翻转图片就是将图片的每一行都进行翻转，即逆序。

例如，水平翻转 [1,1,0] 的结果是 [0,1,1]。
反转图片的意思是图片中的 0 全部被 1 替换， 1 全部被 0 替换。

例如，反转 [0,1,1] 的结果是 [1,0,0]。


```
impl Solution {
    /// 水平翻转并反转图像
    /// 时间复杂度: O(n²)  空间复杂度: O(1)
    pub fn flip_and_invert_image(mut image: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        for row in &mut image {
            let (mut l, mut r) = (0, row.len().saturating_sub(1));

            // 双指针从两端向中间靠拢
            while l < r {
                // 交换并同时反转两个元素
                let tmp = row[l] ^ 1;
                row[l] = row[r] ^ 1;
                row[r] = tmp;

                l += 1;
                r -= 1;
            }

            // 若行长度为奇数，中间元素只需反转，无需交换
            if l == r {
                row[l] ^= 1;
            }
        }
        image
    }
}
```
