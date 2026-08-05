---
title: "leetcode-滑动窗口49"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 考试的最大困扰度

一位老师正在出一场由 n 道判断题构成的考试，每道题的答案为 true （用 'T' 表示）或者 false （用 'F' 表示）。老师想增加学生对自己做出答案的不确定性，方法是 最大化 有 连续相同 结果的题数。（也就是连续出现 true 或者连续出现 false）。

给你一个字符串 answerKey ，其中 answerKey[i] 是第 i 个问题的正确结果。除此以外，还给你一个整数 k ，表示你能进行以下操作的最多次数：

每次操作中，将问题的正确答案改为 'T' 或者 'F' （也就是将 answerKey[i] 改为 'T' 或者 'F' ）。
请你返回在不超过 k 次操作的情况下，最大 连续 'T' 或者 'F' 的数目。




```
impl Solution {
    pub fn max_consecutive_answers(answer_key: String, k: i32) -> i32 {
        let s = answer_key.as_bytes();
        let k = k as usize;
        let n = s.len();
        let mut ans = 0;

        // 分别尝试将窗口变为全 'T' 或全 'F'
        // 对每个目标字符，使用滑动窗口计算最多连续数量
        for target in [b'T', b'F'] {
            let mut left = 0;
            let mut changes = 0;  // 窗口中不等于目标字符的数量（需要翻转的次数）

            for right in 0..n {
                if s[right] != target {
                    changes += 1;
                }

                // 如果翻转次数超过k，移动左边界
                while changes > k {
                    if s[left] != target {
                        changes -= 1;
                    }
                    left += 1;
                }

                ans = ans.max(right - left + 1);
            }
        }

        ans as i32
    }
}
```
