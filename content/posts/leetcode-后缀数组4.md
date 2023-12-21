---
title: "Leetcode 后缀数组4"
date: 2023-12-19T15:46:47+08:00
tags: ["leetcode", "后缀数组"]
draft: false
---

## 最长重复子串

给你一个字符串 s ，考虑其所有 重复子串 ：即 s 的（连续）子串，在 s 中出现 2 次或更多次。这些出现之间可能存在重叠。

返回 任意一个 可能具有最长长度的重复子串。如果 s 不含重复子串，那么答案为 "" 。

```
impl Solution {
    // tp已经排好，开始排序第一关键字
    fn sort(sa: &mut Vec<usize>, rank: &mut Vec<usize>, tp: &Vec<usize>, n: usize, m: usize) {
        let mut tax = vec![0; m+1];
        // 第一关键字基数排序
        rank[1..].iter().for_each(|x| tax[*x] += 1);
        // 前缀和，为了后面方便计算第几名
        for i in 1..=m {
            tax[i] += tax[i-1];
        }
        tp[1..].iter().rev().for_each(|&key| {
            // 拿最小的第二关键字对应的后缀key_2，然后通过rank找到他对应的第一关键字所处的level(第一关键字排名)
            let level = rank[key];
            // tax[level]就表示在这里level之下一共有j个数，则后缀(tp[i])排名第j
            // 此时放的是第一关键字相同，第二关键字最大
            // rank其实也是排好的，拿的是前w/2的rank
            let j = tax[level];
            sa[j] = key;
            tax[level] -= 1;
        });
    }
    fn init(s: &str, n: usize, m: usize) -> (Vec<usize>, Vec<usize>, Vec<usize>) {
        // 只有第一关键字直接映射就好
        let mut rank = vec![0usize];
        rank.append(&mut s.bytes().map(|x| (x + 1 - b'a') as usize).collect::<Vec<_>>());
        let tp = (0..=n).collect::<Vec<_>>();
        let mut sa = vec![0; n+1];
        Self::sort(&mut sa, &mut rank, &tp, n, m);
        
        (sa, rank, tp)
    }
    fn suffix_sort(sa: &mut Vec<usize>, rank: &mut Vec<usize>, tp: &mut Vec<usize>, n: usize, mut m: usize) {
        let (mut w, mut p) = (1, 0);
        // 配合最后一个for_each看，当p==n时证明每个后缀的排名都不相同，此时完成排序
        while p < n && w < n {
            // w是当前倍增长度，现在要去求2w
            p = 1; // 下标标记
            // 后面w个放在最前面，因为后w无第二关键字
            (1..=w).for_each(|i| {
                tp[p] = n + i - w;
                p += 1;
            });
            // 排序第二关键字，用第一关键字(前n-w个)来排
            sa.iter().filter(|&x| *x > w).for_each(|x| {
                tp[p] = *x - w;
                p += 1;
            });
            Self::sort(sa, rank, tp, n, m);
            // tp拿到rank,准备下一轮排序对应Self::sort
            tp.swap_with_slice(rank);
            // 接下来更新对应的rank
            rank[sa[1]] = 1;
            p = 1;
            // 从sa[1..]来看 i是sa[k], j是sa[k+1]
            sa[1..].iter().zip(&sa[2..]).for_each(|(&i, &j)| {
                // 如果上一个第二关键字不相等或者第一关键字不想等则排名+1
                // 如果key2没有则是无穷小，这里是0
                // 第一关键字排名，由于已经swap,tp存的就是全部关键字的排名,现在拿到相邻排名的后缀再去找他的第一关键字排名(前w)
                let key1_1 = tp[i];
                let key1_2 = tp[j];
                // 拿到后缀i j对应的第二关键字排名（后w）
                let key2_1 = if i + w <= n { tp[i + w] } else { 0 };
                let key2_2 = if j + w <= n { tp[j + w] } else { 0 };
                if key1_1 != key1_2 || key2_1 != key2_2 {
                    p += 1;
                }
                // 得到sa[k+1]对应的后缀的排名(2w),逐步更新
                rank[j] = p;
            });
            w *= 2;
            m = p;
        }
    }
    fn get_height(s: &[u8], sa: &Vec<usize>, rank: &Vec<usize>, n: usize) -> Vec<usize> {
        let mut k = 0;
        let mut height = vec![0; n+1];
        // 排名第一的没有排名i-1的子串
        (1..=n).filter(|x| rank[*x] != 1).for_each(|i| {
            // 去掉第一个相同字符
            if k > 0 {
                k -= 1;
            }
            // 找到排名在i前一位的后缀下标
            let j = sa[rank[i]-1];
            while i+k <= n && j+k <= n && s[i+k-1] == s[j+k-1] {
                k += 1;
            }
            height[rank[i]] = k;
        });
        height
    }
    fn get_longest_dup_substring(s: &str, height: &Vec<usize>, sa: &Vec<usize>) -> String {
        // 找到最长的
        let (i, len) = height[1..].iter().enumerate().fold((0, 0), |state, x| {
            // 注意height和sa对应，存的是排名第几的后缀，所以要去sa里面找到他的真实下标
            if *x.1 > state.1 {
                (sa[x.0], *x.1)
            } else {
                state
            }
        });
        // 返回字符串
        match len {
            0 => String::new(),
            _ => s[i-1..i-1+len].to_string(),
        }
    }
    pub fn longest_dup_substring(s: String) -> String {
        let m = 27; // 字符集大小
        let n = s.len(); // 字符串长度
        let (mut sa, mut rank, mut tp) = Self::init(&s, n, m);
        Self::suffix_sort(&mut sa, &mut rank, &mut tp, n, m);
        let height = Self::get_height(s.as_bytes(), &sa, &rank, n);
        Self::get_longest_dup_substring(&s, &height, &sa)
    }
}
```