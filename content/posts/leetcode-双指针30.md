---
title: "leetcode-双指针30"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 神奇字符串

神奇字符串 s 仅由 '1' 和 '2' 组成，并需要遵守下面的规则：

将连续相同字符组 '1' 和 '2' 长度的序列连接起来会生成字符串 s 本身。

s 的前几个元素是 s = "1221121221221121122……" 。如果将 s 中连续的若干 1 和 2 进行分组，可以得到 "1 22 11 2 1 22 1 22 11 2 11 22 ......" 。每组中 1 或者 2 的出现次数分别是 "1 2 2 1 1 2 1 2 2 1 2 2 ......" 。上面的出现次数正是 s 自身。

给你一个整数 n ，返回在神奇字符串 s 的前 n 个数字中 1 的数目。


```
impl Solution {
    pub fn magical_string(n: i32) -> i32 {
        if n <= 0 {
            return 0;
        }
        if n <= 3 {
            return 1;
        }

        // 生成神奇字符串：初始为 "122"
        let mut s = vec![1, 2, 2];
        let mut read = 2;      // 读取指针，指向当前要扩展的组
        let mut write = 3;     // 写入指针，指向下一个要填充的位置
        let mut next_num = 1;  // 下一个要填充的数字（1 或 2）
        let mut count = 1;     // 已出现的 '1' 数量（初始有 1 个）

        while write < n {
            let group_len = s[read] as usize; // 当前组应该重复的次数

            for _ in 0..group_len {
                s.push(next_num);
                if next_num == 1 && write < n {
                    count += 1;
                }
                write += 1;
                if write >= n {
                    break;
                }
            }

            // 切换数字：1 -> 2 -> 1
            next_num = if next_num == 1 { 2 } else { 1 };
            read += 1;
        }

        count
    }
}
```
