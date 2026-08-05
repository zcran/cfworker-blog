---
title: "leetcode-枚举69"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 连接二进制表示可形成的最大数值

给你一个长度为 3 的整数数组 nums。

现以某种顺序 连接 数组 nums 中所有元素的 二进制表示 ，请你返回可以由这种方法形成的 最大 数值。

注意 任何数字的二进制表示 不含 前导零。


```
impl Solution {
    pub fn max_good_number(nums: Vec<i32>) -> i32 {
        // 将三个数字转为二进制字符串
        let bins: Vec<String> = nums.iter().map(|&x| format!("{:b}", x)).collect();

        // 比较两个字符串拼接后的字典序，用于排序
        fn cmp_concat(a: &str, b: &str) -> std::cmp::Ordering {
            let ab = format!("{}{}", a, b);
            let ba = format!("{}{}", b, a);
            ab.cmp(&ba).reverse() // 降序排列
        }

        // 构造所有 6 种排列，找到最大拼接结果
        let mut max_str = String::new();
        for i in 0..3 {
            for j in 0..3 {
                if i == j { continue; }
                for k in 0..3 {
                    if k == i || k == j { continue; }
                    let candidate = format!("{}{}{}", bins[i], bins[j], bins[k]);
                    if candidate > max_str {
                        max_str = candidate;
                    }
                }
            }
        }

        // 二进制字符串转整数
        i32::from_str_radix(&max_str, 2).unwrap()
    }
}
```
