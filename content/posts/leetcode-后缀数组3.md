---
title: "Leetcode 后缀数组3"
date: 2023-12-19T15:46:47+08:00
tags: ["leetcode", "后缀数组"]
draft: false
---

## 最长公共子路径

一个国家由 n 个编号为 0 到 n - 1 的城市组成。在这个国家里，每两个 城市之间都有一条道路连接。

总共有 m 个编号为 0 到 m - 1 的朋友想在这个国家旅游。他们每一个人的路径都会包含一些城市。每条路径都由一个整数数组表示，每个整数数组表示一个朋友按顺序访问过的城市序列。同一个城市在一条路径中可能 重复 出现，但同一个城市在一条路径中不会连续出现。

给你一个整数 n 和二维数组 paths ，其中 paths[i] 是一个整数数组，表示第 i 个朋友走过的路径，请你返回 每一个 朋友都走过的 最长公共子路径 的长度，如果不存在公共子路径，请你返回 0 。

一个 子路径 指的是一条路径中连续的城市序列。

```
fn sa_is(s: &[usize], upper: usize) -> Vec<usize> {
    let n = s.len();
    match n {
        0 => return vec![],
        1 => return vec![0],
        2 => return if s[0] < s[1] { vec![0, 1] } else { vec![1, 0] },
        _ => (),
    }
    let mut sa = vec![0; n];
    let mut ls = vec![false; n];
    for i in (0..n - 1).rev() {
        ls[i] = if s[i] == s[i + 1] {
            ls[i + 1]
        } else {
            s[i] < s[i + 1]
        };
    }
    let mut sum_l = vec![0; upper + 1];
    let mut sum_s = vec![0; upper + 1];
    for i in 0..n {
        if !ls[i] {
            sum_s[s[i]] += 1;
        } else {
            sum_l[s[i] + 1] += 1;
        }
    }
    for i in 0..=upper {
        sum_s[i] += sum_l[i];
        if i < upper {
            sum_l[i + 1] += sum_s[i];
        }
    }

    // sa's origin is 1.
    let induce = |sa: &mut [usize], lms: &[usize]| {
        for elem in sa.iter_mut() {
            *elem = 0;
        }
        let mut buf = sum_s.clone();
        for &d in lms {
            if d == n {
                continue;
            }
            let old = buf[s[d]];
            buf[s[d]] += 1;
            sa[old] = d + 1;
        }
        buf.copy_from_slice(&sum_l);
        let old = buf[s[n - 1]];
        buf[s[n - 1]] += 1;
        sa[old] = n;
        for i in 0..n {
            let v = sa[i];
            if v >= 2 && !ls[v - 2] {
                let old = buf[s[v - 2]];
                buf[s[v - 2]] += 1;
                sa[old] = v - 1;
            }
        }
        buf.copy_from_slice(&sum_l);
        for i in (0..n).rev() {
            let v = sa[i];
            if v >= 2 && ls[v - 2] {
                buf[s[v - 2] + 1] -= 1;
                sa[buf[s[v - 2] + 1]] = v - 1;
            }
        }
    };
    // origin: 1
    let mut lms_map = vec![0; n + 1];
    let mut m = 0;
    for i in 1..n {
        if !ls[i - 1] && ls[i] {
            lms_map[i] = m + 1;
            m += 1;
        }
    }
    let mut lms = Vec::with_capacity(m);
    for i in 1..n {
        if !ls[i - 1] && ls[i] {
            lms.push(i);
        }
    }
    assert_eq!(lms.len(), m);
    induce(&mut sa, &lms);

    if m > 0 {
        let mut sorted_lms = Vec::with_capacity(m);
        for &v in &sa {
            if lms_map[v - 1] != 0 {
                sorted_lms.push(v - 1);
            }
        }
        let mut rec_s = vec![0; m];
        let mut rec_upper = 0;
        rec_s[lms_map[sorted_lms[0]] - 1] = 0;
        for i in 1..m {
            let mut l = sorted_lms[i - 1];
            let mut r = sorted_lms[i];
            let end_l = if lms_map[l] < m { lms[lms_map[l]] } else { n };
            let end_r = if lms_map[r] < m { lms[lms_map[r]] } else { n };
            let same = if end_l - l != end_r - r {
                false
            } else {
                while l < end_l {
                    if s[l] != s[r] {
                        break;
                    }
                    l += 1;
                    r += 1;
                }
                l != n && s[l] == s[r]
            };
            if !same {
                rec_upper += 1;
            }
            rec_s[lms_map[sorted_lms[i]] - 1] = rec_upper;
        }

        let rec_sa = sa_is(&rec_s, rec_upper);
        for i in 0..m {
            sorted_lms[i] = lms[rec_sa[i]];
        }
        induce(&mut sa, &mut sorted_lms);
    }
    for elem in sa.iter_mut() {
        *elem -= 1;
    }
    sa
}

fn sa_is_i32(s: &[i32], upper: i32) -> Vec<usize> {
    let s: Vec<usize> = s.iter().map(|&x| x as usize).collect();
    sa_is(&s, upper as usize)
}

fn suffix_array(s: &[i32], upper: i32) -> Vec<usize> {
    assert!(upper >= 0);
    for &elem in s {
        assert!(0 <= elem && elem <= upper);
    }
    sa_is_i32(s, upper)
}

fn lcp_array<T: Ord>(s: &[T], sa: &[usize]) -> Vec<usize> {
    let n = s.len();
    assert!(n >= 1);
    let mut rnk = vec![0; n];
    for i in 0..n {
        rnk[sa[i]] = i;
    }
    let mut lcp = vec![0; n - 1];
    let mut h = 0;
    for i in 0..n - 1 {
        if h > 0 {
            h -= 1;
        }
        if rnk[i] == 0 {
            continue;
        }
        let j = sa[rnk[i] - 1];
        while j + h < n && i + h < n {
            if s[j + h] != s[i + h] {
                break;
            }
            h += 1;
        }
        lcp[rnk[i] - 1] = h;
    }
    lcp
}

// 以上模板代码修改自[ac-library-rs](https://github.com/rust-lang-ja/ac-library-rs/blob/master/src/string.rs)

impl Solution {
    pub fn longest_common_subpath(n: i32, paths: Vec<Vec<i32>>) -> i32 {
        let m = paths.len();
        let mut hi = paths.iter().map(|x| x.len()).min().unwrap();
        let mut lo = 1;

        let mut global_path = vec![];
        let mut belong = vec![];
        let mut sep = n;
        
        // 将 m 条路径连成一条总路径，并用分隔符分隔
        for path in paths.iter() {
            for &city in path.iter() {
                global_path.push(city);
                belong.push(sep - n);
            }
            global_path.push(sep);
            belong.push(sep - n);
            
            // 使用的分隔符是 m 个不属于 [0,n) 且各不相同的数字，这样可以避免两个后缀的最长公共前缀跨越不同的路径
            sep += 1;
        }

        // sa[i] 表示排名为 i 的是哪一个后缀
        let sa = suffix_array(&global_path, sep - 1);

        // lcp[i] 表示排第 i 名和第 i+1 名的后缀的最长公共前缀的长度
        let lcp = lcp_array(&global_path, &sa);

        // 二分答案
        while lo <= hi {
            let mid = (lo + hi) >> 1;
            let mut i = 0;
            let mut found = false;

            while i < lcp.len() {
                if lcp[i] >= mid {
                    let mut vis = vec![false; m];
                    let mut vis_count = 0;
                    let mut j = i;
                    
                    // 找出不小于 mid 的最长的一段 lcp[i..j]，统计这些元素的归属，判断是否覆盖了所有路径。
                    while j < lcp.len() && lcp[j] >= mid {
                        if !vis[belong[sa[j]] as usize] {
                            vis[belong[sa[j]] as usize] = true;
                            vis_count += 1;
                        }
                        j += 1;
                    }
                    
                    // 因为 lcp[i] 表示 sa[i] 和 sa[i+1] 的最长公共前缀，所以还需要计入最后的一个位置。
                    if !vis[belong[sa[j]] as usize] {
                        vis_count += 1;
                    }

                    // 当前范围包含了 m 条路径，说明找到了长度为 mid 的公共子路径。
                    if vis_count == m {
                        found = true;
                        break;
                    }

                    i = j;
                } else {
                    i += 1;
                }
            }

            if found {
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }

        hi as i32
    }
}
```