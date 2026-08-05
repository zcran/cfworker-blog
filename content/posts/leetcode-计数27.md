---
title: "leetcode-计数27"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 数青蛙

给你一个字符串 croakOfFrogs，它表示不同青蛙发出的蛙鸣声（字符串 "croak" ）的组合。由于同一时间可以有多只青蛙呱呱作响，所以 croakOfFrogs 中会混合多个 “croak” 。

请你返回模拟字符串中所有蛙鸣所需不同青蛙的最少数目。

要想发出蛙鸣 "croak"，青蛙必须 依序 输出 ‘c’, ’r’, ’o’, ’a’, ’k’ 这 5 个字母。如果没有输出全部五个字母，那么它就不会发出声音。如果字符串 croakOfFrogs 不是由若干有效的 "croak" 字符混合而成，请返回 -1 。


```
impl Solution {
    pub fn min_number_of_frogs(croak_of_frogs: String) -> i32 {
        // 分别统计每个字母的出现次数
        let mut count = [0; 5]; // c, r, o, a, k
        // 当前正在叫的青蛙数量，以及历史最大同时叫的青蛙数
        let mut current = 0;
        let mut max_frogs = 0;

        for ch in croak_of_frogs.bytes() {
            match ch {
                b'c' => {
                    count[0] += 1;
                    current += 1; // 开始叫，占用一只青蛙
                }
                b'r' => {
                    count[1] += 1;
                    // 检查顺序是否合法：r 不能多于 c
                    if count[1] > count[0] {
                        return -1;
                    }
                }
                b'o' => {
                    count[2] += 1;
                    if count[2] > count[1] {
                        return -1;
                    }
                }
                b'a' => {
                    count[3] += 1;
                    if count[3] > count[2] {
                        return -1;
                    }
                }
                b'k' => {
                    count[4] += 1;
                    if count[4] > count[3] {
                        return -1;
                    }
                    current -= 1; // 完成叫声，释放青蛙
                }
                _ => return -1, // 出现无效字符
            }

            // 更新高峰时的青蛙数量
            max_frogs = max_frogs.max(current);
        }

        // 检查是否所有字母数量相等（完整形成若干 "croak"）
        if count[0] == count[1]
            && count[1] == count[2]
            && count[2] == count[3]
            && count[3] == count[4]
        {
            max_frogs
        } else {
            -1
        }
    }
}
```
