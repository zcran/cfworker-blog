---
title: "Rust Note 16"
date: 2023-05-22T20:28:46+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 验证回文串

如果在将所有大写字符转换为小写字符、并移除所有非字母数字字符之后，短语正着读和反着读都一样。则可以认为该短语是一个 回文串 。

字母和数字都属于字母数字字符。

给你一个字符串 s，如果它是 回文串 ，返回 true ；否则，返回 false 。


### C 解法：
```
class Solution {
public:
    bool isPalindrome(string s) {
        int n = s.size();
        int left = 0, right = n - 1;
        while (left < right) {
            while (left < right && !isalnum(s[left])) {
                ++left;
            }
            while (left < right && !isalnum(s[right])) {
                --right;
            }
            if (left < right) {
                if (tolower(s[left]) != tolower(s[right])) {
                    return false;
                }
                ++left;
                --right;
            }
        }
        return true;
    }
};
```


### Rust 解法：
```
impl Solution {
    pub fn is_palindrome(s: String) -> bool {
        let s = s
            .chars()
            .filter(|c| c.is_ascii_alphabetic() || c.is_ascii_digit())
            .map(|c| c.to_ascii_lowercase())
            .collect::<Vec<_>>();
        (0..s.len() / 2).all(|i| s[i] == s[s.len() - 1 - i])
    }
}
```

### Rust all()函数
fn all<F>(&mut self, f: F) -> bool
where
    F: FnMut(Self::Item) -> bool, 
测试迭代器的每个元素是否与谓词匹配。

all() 接受一个返回 true 或 false 的闭包。它将这个闭包应用于迭代器的每个元素，如果它们都返回 true，那么 all() 也返回。 如果它们中的任何一个返回 false，则返回 false。

all() 是短路的； 换句话说，它一旦找到 false 就会停止处理，因为无论发生什么，结果也将是 false。

空的迭代器将返回 true。

Examples：
```
let a = [1, 2, 3];
assert!(a.iter().all(|&x| x > 0));
assert!(!a.iter().all(|&x| x > 2));
```

在第一个 false 处停止：
```
let a = [1, 2, 3];
let mut iter = a.iter();
assert!(!iter.all(|&x| x != 2));
// 我们仍然可以使用 `iter`，因为还有更多元素。
assert_eq!(iter.next(), Some(&3));
```