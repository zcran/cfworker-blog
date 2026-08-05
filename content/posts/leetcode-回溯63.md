---
title: "leetcode-回溯63"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 求一个整数的惩罚数

给你一个正整数 n ，请你返回 n 的 惩罚数 。

n 的 惩罚数 定义为所有满足以下条件 i 的数的平方和：

1 <= i <= n

i * i 的十进制表示的字符串可以分割成若干连续子字符串，且这些子字符串对应的整数值之和等于 i 。


```
impl Solution {
    pub fn punishment_number(n: i32) -> i32 {
        // 预计算所有惩罚数（1..=1000）
        const MAX_N: usize = 1000;
        let mut pre_sum = [0; MAX_N + 1];

        for i in 1..=MAX_N {
            let square = (i * i) as usize;
            let s = square.to_string();
            let digits: Vec<u8> = s.bytes().collect();

            // 检查 i*i 是否可以分割成若干子串，其和等于 i
            if Self::can_partition(&digits, 0, 0, i as i32) {
                pre_sum[i] = pre_sum[i - 1] + square as i32;
            } else {
                pre_sum[i] = pre_sum[i - 1];
            }
        }

        pre_sum[n as usize]
    }

    /// 检查是否可以分割字符串，使得各部分数字之和等于 target
    fn can_partition(s: &[u8], pos: usize, sum: i32, target: i32) -> bool {
        if pos == s.len() {
            return sum == target;
        }

        let mut num = 0;
        for j in pos..s.len() {
            // 将字节转换为数字 (0-9)
            num = num * 10 + (s[j] - b'0') as i32;

            // 剪枝：如果当前和已经超过目标，提前返回
            if sum + num > target {
                break;
            }

            if Self::can_partition(s, j + 1, sum + num, target) {
                return true;
            }
        }
        false
    }
}
```


```
impl Solution {
    pub fn punishment_number(n: i32) -> i32 {
        let dict = vec![
            1, 9, 10, 36, 45, 55, 82, 91, 99, 100, 235, 297, 369, 370, 379, 414, 657, 675, 703,
            756, 792, 909, 918, 945, 964, 990, 991, 999, 1000,
        ];
        let mut prefix = Vec::with_capacity(dict.len());
        let mut sum = 0;
        for num in dict.clone() {
            sum += num * num;
            prefix.push(sum);
        }
        let index = dict.partition_point(|x| *x <= n);
        prefix[index - 1]
    }
}
```
