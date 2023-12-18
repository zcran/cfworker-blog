---
title: "Rust Note 48"
date: 2023-06-12T10:35:06+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
每个右括号都有一个对应的相同类型的左括号。

### C 解法：
```
class Solution {
public:
    bool isValid(string s) {
        int n = s.size();
        if (n % 2 == 1) {
            return false;
        }

        unordered_map<char, char> pairs = {
            {')', '('},
            {']', '['},
            {'}', '{'}
        };
        stack<char> stk;
        for (char ch: s) {
            if (pairs.count(ch)) {
                if (stk.empty() || stk.top() != pairs[ch]) {
                    return false;
                }
                stk.pop();
            }
            else {
                stk.push(ch);
            }
        }
        return stk.empty();
    }
};
```


### Rust 解法：
```
impl Solution {
     pub fn is_valid(s: String) -> bool {
        let mut stack = vec![];
        for c in s.chars(){
            match c {
                '{'=>stack.push('}'),
                '['=>stack.push(']'),
                '('=>stack.push(')'),
                '}'|']'|')' if Some(c) != stack.pop() => return false,
                _=>(),
            }
        }
        stack.is_empty()
    }
}
```