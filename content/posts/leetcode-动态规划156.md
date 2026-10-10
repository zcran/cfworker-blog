---
title: "leetcode-动态规划156"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 字符串转换需要的最小操作数

给你两个长度相等的字符串 word1 和 word2。你的任务是将 word1 转换成 word2。

为此，可以将 word1 分割成一个或多个连续子字符串。对于每个子字符串 substr，可以执行以下操作：

1. 替换：将 substr 中任意一个索引处的字符替换为另一个小写字母。

2. 交换：交换 substr 中任意两个字符的位置。

3. 反转子串：将 substr 进行反转。

每种操作计为 一次 ，并且每个子串中的每个字符在每种操作中最多只能使用一次（即任何字符的下标不能参与超过一次替换、交换或反转操作）。

返回将 word1 转换为 word2 所需的 最小操作数 。

子串 是字符串中任意一个连续且非空的字符序列。


```
impl Solution {
    pub fn min_operations(s: String, t: String) -> i32 {
        let s = s.as_bytes();
        let t = t.as_bytes();
        let n = s.len();
        let inf = i32::MAX / 2;

        // update: 用配对的方式统计把 s[x] 变成 t[y] 的最小操作次数
        // cnt[a][b] 表示当前待处理的 "a -> b" 转换计数
        // 若已有反向配对 "b -> a"，则可与其配对（一次交换/替换即可），操作数不变
        #[inline]
        fn update(cnt: &mut [[i32; 26]; 26], op: &mut i32, x: u8, y: u8) {
            if x == y {
                return;
            }
            let (a, b) = ((x - b'a') as usize, (y - b'a') as usize);
            if cnt[b][a] > 0 {
                cnt[b][a] -= 1;
            } else {
                cnt[a][b] += 1;
                *op += 1;
            }
        }

        // rev_op[l][r]：把子串 s[l..=r] 通过"反转 + 逐位修正"变成 t[l..=r] 所需的最少操作数
        // 反转后 s[l..=r] 与 t[l..=r] 的对应关系是 s[l+i] -> t[r-i]
        // 用中心扩展法预处理所有回文中心，得到所有子串的反转操作数
        let mut rev_op = vec![vec![0i32; n]; n];
        for i in 0..2 * n - 1 {
            let mut cnt = [[0i32; 26]; 26];
            let mut op = 1; // 反转本身算一次操作
            let mut l = (i / 2) as isize;
            let mut r = (i + 1) / 2;
            while l >= 0 && r < n {
                let li = l as usize;
                update(&mut cnt, &mut op, s[li], t[r]);
                if li != r {
                    update(&mut cnt, &mut op, s[r], t[li]);
                }
                rev_op[li][r] = op;
                l -= 1;
                r += 1;
            }
        }

        // f[i]：将 s[..i] 转换为 t[..i] 所需的最小操作数
        let mut f = vec![0i32; n + 1];
        for i in 0..n {
            let mut res = inf;
            let mut cnt = [[0i32; 26]; 26];
            let mut op = 0; // 不反转时，把 s[j..=i] 变成 t[j..=i] 所需操作数
            for j in (0..=i).rev() {
                update(&mut cnt, &mut op, s[j], t[j]);
                // 对区间 [j..=i]，可以选择不反转（op）或反转（rev_op[j][i]）
                res = res.min(f[j] + op.min(rev_op[j][i]));
            }
            f[i + 1] = res;
        }
        f[n]
    }
}
```
