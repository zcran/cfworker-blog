---
title: "Leetcode 迭代器4"
date: 2023-12-19T15:49:00+08:00
tags: ["leetcode", "迭代器"]
draft: false
---

## 字母组合迭代器

请你设计一个迭代器类 CombinationIterator ，包括以下内容：

CombinationIterator(string characters, int combinationLength) 一个构造函数，输入参数包括：用一个 有序且字符唯一 的字符串 characters（该字符串只包含小写英文字母）和一个数字 combinationLength 。
函数 next() ，按 字典序 返回长度为 combinationLength 的下一个字母组合。
函数 hasNext() ，只有存在长度为 combinationLength 的下一个字母组合时，才返回 true

```
struct CombinationIterator {
    s: Vec<u8>,
    pos: Vec<usize>,
    has_next: bool,
}

impl CombinationIterator {
    fn new(characters: String, len: i32) -> Self {
        let mut s = characters.into_bytes();
        s.sort_unstable();
        Self {
            s,
            pos: (0..len as usize).collect(),
            has_next: true,
        }
    }

    fn next(&mut self) -> String {
        fn dfs(pos: &mut Vec<usize>, cur: usize, len: usize, has_next: &mut bool) -> usize {
            let i = pos.len() - cur - 1;
            if pos[i] + 1 == len - cur {
                if i == 0 {
                    *has_next = false;
                } else {
                    pos[i] = dfs(pos, cur + 1, len, has_next) + 1;
                }
            } else {
                pos[i] += 1;
            }
            pos[i]
        }
        let mut ans = Vec::with_capacity(self.pos.len());
        self.pos.iter().for_each(|&i| ans.push(self.s[i]));
        let len = self.s.len();
        dfs(&mut self.pos, 0, len, &mut self.has_next);
        unsafe { String::from_utf8_unchecked(ans) }
    }

    fn has_next(&self) -> bool {
        self.has_next
    }
}

```