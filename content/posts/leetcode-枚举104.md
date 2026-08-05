---
title: "leetcode-枚举104"
date: 2026-07-09T10:05:04+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 文件组合

待传输文件被切分成多个部分，按照原排列顺序，每部分文件编号均为一个 正整数（至少含有两个文件）。传输要求为：连续文件编号总和为接收方指定数字 target 的所有文件。请返回所有符合该要求的文件传输组合列表。

注意，返回时需遵循以下规则：

每种组合按照文件编号 升序 排列；

不同组合按照第一个文件编号 升序 排列。


```
impl Solution {
    pub fn file_combination(target: i32) -> Vec<Vec<i32>> {
        let mut ans = Vec::new();
        let mut left = 1;

        // 连续正整数的和，左边界最大为 (target - 1) / 2
        while left <= (target - 1) / 2 {
            let mut right = left + 1;
            // 右边界最大为 (target + 1) / 2
            while right <= (target + 1) / 2 {
                // 等差数列求和公式：(首项 + 末项) * 项数 / 2
                let sum = (left + right) * (right - left + 1) / 2;

                if sum > target {
                    break; // 由于 right 增大，sum 会继续增大，提前终止
                }

                if sum == target {
                    // 收集当前连续序列
                    ans.push((left..=right).collect());
                }
                right += 1;
            }
            left += 1;
        }

        ans
    }
}
```
