---
title: "Rust Note 18"
date: 2023-05-22T20:28:46+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 实现strStr()

给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串的第一个匹配项的下标（下标从 0 开始）。如果 needle 不是 haystack 的一部分，则返回  -1 。


### C 解法：
```
   // 暴力匹配
class Solution {
public:
    int strStr(string haystack, string needle) {
        int n = haystack.size(), m = needle.size();
        for (int i = 0; i + m <= n; i++) {
            bool flag = true;
            for (int j = 0; j < m; j++) {
                if (haystack[i + j] != needle[j]) {
                    flag = false;
                    break;
                }
            }
            if (flag) {
                return i;
            }
        }
        return -1;
    }
};
```


### Rust 解法1：
```
  // KMP算法
impl Solution {
    pub fn str_str(haystack: String, needle: String) -> i32 {
        let haystack = haystack.into_bytes();
        let needle = needle.into_bytes();
        let H = haystack.len();
        let N = needle.len();

        if N == 0 {
            return 0;
        }

        let mut next = vec![0];

        for i in 1..N {
            let mut j = next[i - 1];
            while j > 0 && needle[i] != needle[j] {
                j = next[j - 1];
            }

            next.push(if needle[i] == needle[j] { j + 1 } else { j });
        }

        let mut j = 0;

        for (i, &c) in haystack.iter().enumerate() {
            while j > 0 && c != needle[j] {
                j = next[j - 1];
            }

            if needle[j] == c {
                j += 1;
            }

            if j == N {
                return (i + 1 - j) as i32;
            }
        }

        -1
    }    
}
```

### Rust解法2:
```
impl Solution {
    pub fn str_str(haystack: String, needle: String) -> i32 {
        match haystack.find(&needle) {
            Some(idx) => idx as i32,
            None => -1,
        }
    }
}
```

### Rust find()函数：
pub fn find<'a, P>(&'a self, pat: P) -> Option<usize>
where
    P: Pattern<'a>, 
返回此字符串切片中与模式匹配的第一个字符的字节索引。

如果模式不匹配，则返回 None。

模式 可以是 &str，char，char 的切片，也可以是确定字符是否匹配的函数或闭包。

Examples：
```
let s = "Löwe 老虎 Léopard Gepardi";

assert_eq!(s.find('L'), Some(0));
assert_eq!(s.find('é'), Some(14));
assert_eq!(s.find("pard"), Some(17));
```

使用无点样式和闭包的更复杂的模式：
```
let s = "Löwe 老虎 Léopard";

assert_eq!(s.find(char::is_whitespace), Some(5));
assert_eq!(s.find(char::is_lowercase), Some(1));
assert_eq!(s.find(|c: char| c.is_whitespace() || c.is_lowercase()), Some(1));
assert_eq!(s.find(|c: char| (c < 'o') && (c > 'a')), Some(4));
```

找不到模式：
```
let s = "Löwe 老虎 Léopard";
let x: &[_] = &['1', '2'];

assert_eq!(s.find(x), None);
```