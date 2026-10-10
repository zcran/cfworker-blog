---
title: "leetcode-动态规划123"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 执行操作使两个字符串相等

给你两个下标从 0 开始的二进制字符串 s1 和 s2 ，两个字符串的长度都是 n ，再给你一个正整数 x 。

你可以对字符串 s1 执行以下操作 任意次 ：

选择两个下标 i 和 j ，将 s1[i] 和 s1[j] 都反转，操作的代价为 x 。

选择满足 i < n - 1 的下标 i ，反转 s1[i] 和 s1[i + 1] ，操作的代价为 1 。

请你返回使字符串 s1 和 s2 相等的 最小 操作代价之和，如果无法让二者相等，返回 -1 。

注意 ，反转字符的意思是将 0 变成 1 ，或者 1 变成 0 。


```
impl Solution {
    pub fn min_operations(s1: String, s2: String, x: i32) -> i32 {
        let s1 = s1.as_bytes();
        let s2 = s2.as_bytes();
        let n = s1.len();

        // 若两个字符串中 '1' 的个数奇偶性不同，无法通过操作使其相等
        let cnt1 = s1.iter().filter(|&&b| b == b'1').count();
        let cnt2 = s2.iter().filter(|&&b| b == b'1').count();
        if cnt1 % 2 != cnt2 % 2 {
            return -1;
        }

        const INF: i32 = i32::MAX / 2;

        // 采用 DFS + 记忆化：
        // 从右往左处理，j 表示已累积的「待配对反转向量」数量，
        // pre_rev 表示右邻居的翻转是否通过代价 1 的操作传递到当前位
        fn dfs(
            i: isize, j: usize, pre_rev: bool,
            s1: &[u8], s2: &[u8], x: i32,
            memo: &mut Vec<Vec<[i32; 2]>>,
        ) -> i32 {
            if i < 0 {
                // 全部处理完毕：待配对数与传递标记都必须为空
                return if j == 0 && !pre_rev { 0 } else { INF };
            }
            let idx = i as usize;
            let p = pre_rev as usize;
            if memo[idx][j][p] != -1 {
                return memo[idx][j][p];
            }

            // 判断当前位在考虑 pre_rev 后是否还需要额外反转
            // (s1==s2) 表示原本相同；pre_rev 会把这一位的实际状态翻转
            // 若 (s1==s2) == !pre_rev，说明无需再反转
            let no_rev_needed = (s1[idx] == s2[idx]) == !pre_rev;

            let res = if no_rev_needed {
                dfs(i - 1, j, false, s1, s2, x, memo)
            } else {
                let mut best = INF;

                // 选择一：与某个远端位置配对，代价 x，新增一个待配对请求
                let a = dfs(i - 1, j + 1, false, s1, s2, x, memo);
                if a < INF {
                    best = best.min(a + x);
                }

                // 选择二：使用代价 1 的相邻反转，把反转传递给左邻居（pre_rev 置 true）
                let b = dfs(i - 1, j, true, s1, s2, x, memo);
                if b < INF {
                    best = best.min(b + 1);
                }

                // 选择三：若存在待配对的请求，则当前位置与之前某个位置配对（免费）
                if j > 0 {
                    let c = dfs(i - 1, j - 1, false, s1, s2, x, memo);
                    if c < INF {
                        best = best.min(c);
                    }
                }

                best
            };

            memo[idx][j][p] = res;
            res
        }

        let mut memo = vec![vec![[-1i32; 2]; n + 1]; n];
        dfs(n as isize - 1, 0, false, s1, s2, x, &mut memo)
    }
}
```
