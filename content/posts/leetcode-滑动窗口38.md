---
title: "leetcode-滑动窗口38"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 拆炸弹

你有一个炸弹需要拆除，时间紧迫！你的情报员会给你一个长度为 n 的 循环 数组 code 以及一个密钥 k 。

为了获得正确的密码，你需要替换掉每一个数字。所有数字会 同时 被替换。

如果 k > 0 ，将第 i 个数字用 接下来 k 个数字之和替换。
如果 k < 0 ，将第 i 个数字用 之前 k 个数字之和替换。
如果 k == 0 ，将第 i 个数字用 0 替换。

由于 code 是循环的， code[n-1] 下一个元素是 code[0] ，且 code[0] 前一个元素是 code[n-1] 。

给你 循环 数组 code 和整数密钥 k ，请你返回解密后的结果来拆除炸弹！


```
impl Solution {
    pub fn decrypt(code: Vec<i32>, k: i32) -> Vec<i32> {
        let n = code.len();
        let mut ans = vec![0; n];

        if k == 0 {
            return ans;
        }

        let k_abs = k.abs() as usize;
        let mut window_sum = 0;

        // 初始化窗口：k>0 时窗口在右边，k<0 时窗口在左边
        if k > 0 {
            // 计算 code[1..=k] 的和
            for i in 1..=k_abs {
                window_sum += code[i % n];
            }
        } else {
            // 计算 code[n-k..=n-1] 的和（即最后 k 个元素）
            for i in n - k_abs..n {
                window_sum += code[i];
            }
        }

        // 滑动窗口，替换每个位置
        for i in 0..n {
            ans[i] = window_sum;

            // 窗口向右滑动一步
            if k > 0 {
                // 移除 code[i+1]，加入 code[i+1+k_abs]
                window_sum -= code[(i + 1) % n];
                window_sum += code[(i + 1 + k_abs) % n];
            } else {
                // 移除 code[i-k_abs]，加入 code[i]
                // 注意：窗口始终是当前位置左边的 k_abs 个元素
                window_sum -= code[(i + n - k_abs) % n];
                window_sum += code[i];
            }
        }

        ans
    }
}
```
