---
title: "leetcode-双指针136"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 工位的最大间隔

给你两个长度分别为 n 和 m 的字符串 skill 和 station。

skill[i] 表示工人 i 的技能，station[j] 表示工位 j 所支持的技能。

你必须将每一名工人分配到一个互不相同的工位。令 ji 表示分配给工人 i 的工位下标。有效的分配方案必须满足：

对于每个 0 <= i < n，都有 station[ji] == skill[i]。
按照工人的顺序，分配的工位下标必须严格递增，即 j0 < j1 < ... < jn - 1。

分配方案的间隔是分配给两名相邻工人的工位下标之间的最大差值。换句话说，它等于所有 1 <= i < n 中 ji - ji - 1 的最大值。

如果只有一名工人，则间隔为 0。

返回所有有效分配方案中可能得到的最大间隔。题目保证至少存在一种有效的分配方案。


```
impl Solution {
    pub fn maximum_gap(skill: String, station: String) -> i32 {
        let s = skill.as_bytes();
        let t = station.as_bytes();
        let n = s.len();
        let m = t.len();

        // suf[i]：s[i..] 作为 t 的子序列时，贪心匹配到的最左结束位置
        // 即从右往左，s[i..] 能匹配 t[suf[i]..]
        let mut suf = vec![0usize; n];
        let mut j = m - 1;
        for i in (1..n).rev() {
            while t[j] != s[i] {
                j -= 1;
            }
            suf[i] = j;
            j -= 1;
        }

        let mut ans = 0usize;
        let mut pre = 0usize;
        for i in 0..n - 1 {
            // 贪心从前往后匹配 s[i]，得到 s[..=i] 匹配 t[..=pre]
            while t[pre] != s[i] {
                pre += 1;
            }
            // 相邻工位下标的最大差值：suf[i+1] - pre
            ans = ans.max(suf[i + 1] - pre);
            pre += 1;
        }

        ans as i32
    }
}
```
