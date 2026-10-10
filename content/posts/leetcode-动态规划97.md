---
title: "leetcode-动态规划97"
date: 2026-10-05T20:40:00+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 同源字符串检测

原字符串由小写字母组成，可以按下述步骤编码：

· 任意将其 分割 为由若干 非空 子字符串组成的一个 序列 。
· 任意选择序列中的一些元素（也可能不选择），然后将这些元素替换为元素各自的长度（作为一个数字型的字符串）。
· 重新 顺次连接 序列，得到编码后的字符串。

例如，编码 "abcdefghijklmnop" 的一种方法可以描述为：

· 将原字符串分割得到一个序列：["ab", "cdefghijklmn", "o", "p"] 。
· 选出其中第二个和第三个元素并分别替换为它们自身的长度。序列变为 ["ab", "12", "1", "p"] 。
· 重新顺次连接序列中的元素，得到编码后的字符串："ab121p" 。

给你两个编码后的字符串 s1 和 s2 ，由小写英文字母和数字 1-9 组成。如果存在能够同时编码得到 s1 和 s2 原字符串，返回 true ；否则，返回 false。

注意：生成的测试用例满足 s1 和 s2 中连续数字数不超过 3 。


```
impl Solution {
    pub fn possibly_equals(s1: String, s2: String) -> bool {
        let s1 = s1.as_bytes();
        let s2 = s2.as_bytes();
        let m = s1.len();
        let n = s2.len();
        // 最大“待匹配字符数”不会超过 1000（连续数字 ≤ 3 位，最大 999）
        const MAX_REST: usize = 1000;

        // memo[i][j][which][rest]：记忆化，0 表示未访问，1 表示 true，2 表示 false
        // which = 0 表示当前需要消耗 s2 的字符（s1 在等待），
        // which = 1 表示当前需要消耗 s1 的字符（s2 在等待）
        let mut memo = vec![vec![vec![vec![0u8; MAX_REST + 1]; 2]; n + 1]; m + 1];

        // 使用显式栈的 DFS，避免递归带来的闭包捕获问题
        // 返回值为 true 表示可以匹配
        fn dfs(
            i: usize, j: usize, which: usize, rest: usize,
            s1: &[u8], s2: &[u8], m: usize, n: usize,
            memo: &mut Vec<Vec<Vec<Vec<u8>>>>,
        ) -> bool {
            if memo[i][j][which][rest] != 0 {
                return memo[i][j][which][rest] == 1;
            }

            let res = if which == 0 {
                // 当前需要消耗 s2 的字符（或数字表示的长度）
                if j == n {
                    i == m && rest == 0
                } else if s2[j].is_ascii_alphabetic() {
                    if rest == 0 && i != m && s1[i].is_ascii_alphabetic() {
                        s1[i] == s2[j] && dfs(i + 1, j + 1, 0, 0, s1, s2, m, n, memo)
                    } else if rest >= 1 {
                        dfs(i, j + 1, 0, rest - 1, s1, s2, m, n, memo)
                    } else {
                        dfs(i, j + 1, 1, 1, s1, s2, m, n, memo)
                    }
                } else {
                    let mut x = 0usize;
                    let mut k = j;
                    let mut ok = false;
                    while k < n && s2[k].is_ascii_digit() {
                        x = x * 10 + (s2[k] - b'0') as usize;
                        if rest >= x {
                            if dfs(i, k + 1, 0, rest - x, s1, s2, m, n, memo) {
                                ok = true;
                                break;
                            }
                        } else if dfs(i, k + 1, 1, x - rest, s1, s2, m, n, memo) {
                            ok = true;
                            break;
                        }
                        k += 1;
                    }
                    ok
                }
            } else {
                // 当前需要消耗 s1 的字符（或数字表示的长度）
                if i == m {
                    j == n && rest == 0
                } else if s1[i].is_ascii_alphabetic() {
                    if rest == 0 && j != n && s2[j].is_ascii_alphabetic() {
                        s1[i] == s2[j] && dfs(i + 1, j + 1, 0, 0, s1, s2, m, n, memo)
                    } else if rest >= 1 {
                        dfs(i + 1, j, 1, rest - 1, s1, s2, m, n, memo)
                    } else {
                        dfs(i + 1, j, 0, 1, s1, s2, m, n, memo)
                    }
                } else {
                    let mut x = 0usize;
                    let mut k = i;
                    let mut ok = false;
                    while k < m && s1[k].is_ascii_digit() {
                        x = x * 10 + (s1[k] - b'0') as usize;
                        if rest >= x {
                            if dfs(k + 1, j, 1, rest - x, s1, s2, m, n, memo) {
                                ok = true;
                                break;
                            }
                        } else if dfs(k + 1, j, 0, x - rest, s1, s2, m, n, memo) {
                            ok = true;
                            break;
                        }
                        k += 1;
                    }
                    ok
                }
            };

            memo[i][j][which][rest] = if res { 1 } else { 2 };
            res
        }

        dfs(0, 0, 0, 0, s1, s2, m, n, &mut memo)
    }
}
```
