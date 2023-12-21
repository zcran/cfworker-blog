---
title: "Leetcode 欧拉回路2"
date: 2023-12-19T15:20:02+08:00
tags: ["leetcode", "欧拉回路"]
draft: false
---

## 破解保险箱

有一个需要密码才能打开的保险箱。密码是 n 位数, 密码的每一位都是范围 [0, k - 1] 中的一个数字。

保险箱有一种特殊的密码校验方法，你可以随意输入密码序列，保险箱会自动记住 最后 n 位输入 ，如果匹配，则能够打开保险箱。

例如，正确的密码是 "345" ，并且你输入的是 "012345" ：
输入 0 之后，最后 3 位输入是 "0" ，不正确。
输入 1 之后，最后 3 位输入是 "01" ，不正确。
输入 2 之后，最后 3 位输入是 "012" ，不正确。
输入 3 之后，最后 3 位输入是 "123" ，不正确。
输入 4 之后，最后 3 位输入是 "234" ，不正确。
输入 5 之后，最后 3 位输入是 "345" ，正确，打开保险箱。
在只知道密码位数 n 和范围边界 k 的前提下，请你找出并返回确保在输入的 某个时刻 能够打开保险箱的任一 最短 密码序列 。

```
//Rust Hierholzer 算法 
//Hierholzer 算法可以在一个欧拉图中找出欧拉回路。
//把官方的C++题解改成Rust
use std::collections::HashSet;

impl Solution {
    pub fn crack_safe(n: i32, k: i32) -> String {
        let mut seen = HashSet::new();
        let mut ans = String::new();
        let mut highest = 10_i32.pow(n as u32 - 1);
        Self::dfs(0, highest, k, &mut seen, &mut ans);
        ans.push_str(&"0".repeat((n - 1) as usize));
        ans
    }
    fn dfs(node:i32, highest:i32, k:i32, seen:&mut HashSet<i32>, ans:&mut String){
        for x in 0..(k) {
            let mut nei = node * 10 + x;
            if !seen.contains(&nei) {
                seen.insert(nei);
                Self::dfs(nei % highest, highest, k,  seen,  ans);
                ans.push((x as u8 + b'0') as char);
                println!("{:?}",ans);
            }
        }
    }
}
```