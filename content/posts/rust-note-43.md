---
title: "Rust Note 43"
date: 2023-06-12T10:35:05+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 罗马数字转整数

罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。

字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
例如， 罗马数字 2 写做 II ，即为两个并列的 1 。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
给定一个罗马数字，将其转换成整数。

### C 解法：
```
class Solution {
private:
    unordered_map<char, int> symbolValues = {
        {'I', 1},
        {'V', 5},
        {'X', 10},
        {'L', 50},
        {'C', 100},
        {'D', 500},
        {'M', 1000},
    };

public:
    int romanToInt(string s) {
        int ans = 0;
        int n = s.length();
        for (int i = 0; i < n; ++i) {
            int value = symbolValues[s[i]];
            if (i < n - 1 && value < symbolValues[s[i + 1]]) {
                ans -= value;
            } else {
                ans += value;
            }
        }
        return ans;
    }
};
```


### Rust 解法1：
```
 // 宏
impl Solution {
    pub fn roman_to_int(s: String) -> i32 {
        let mut sum = 0;
        let mut chars = s.chars();
        let mut char = if let Some(char) = chars.next() {
            char
        } else {
            return 0;
        };
        loop {
            macro_rules! next {
                () => {
                    char = if let Some(c) = chars.next() {
                        c
                    } else {
                        break;
                    }
                };
                (no_break) => {
                    if let Some(c) = chars.next() {
                        char = c;
                        true
                    } else {
                        false
                    }
                };
            }
            macro_rules! add {
                ( $num:expr ) => {{
                    sum += $num;
                    next!();
                }};
            }
            macro_rules! half_pat {
                ( $self:expr => $(($pat:pat, $add:expr)),+ ) => {{
                    let mut no_stop = next!(no_break);
                    sum += if no_stop {
                        match char {
                            $(
                                $pat => {
                                    no_stop = next!(no_break);
                                    $add
                                },
                            )+
                            _ => $self,
                        }
                    } else {
                        $self
                    };
                    if ! no_stop { break }
                }};
            }
            match char {
                'V' => add!(5),
                'L' => add!(50),
                'D' => add!(500),
                'M' => add!(1000),
                'I' => half_pat!(1 => ('V', 4), ('X', 9)),
                'X' => half_pat!(10 => ('L', 40), ('C', 90)),
                'C' => half_pat!(100 => ('D', 400), ('M', 900)),
                _ => (),
            }
        }
    sum
    }
}
```

### Rust解法2:
```
 // 模式匹配
impl Solution {
    pub fn roman_to_int(s: String) -> i32 {
        s.chars().fold((0, ' '), |res, ch| {
            match (res.1, ch) {
                ('I', 'V') => (res.0 + 3, 'V'),
                ('I', 'X') => (res.0 + 8, 'X'),
                ('X', 'L') => (res.0 + 30, 'L'),
                ('X', 'C') => (res.0 + 80, 'C'),
                ('C', 'D') => (res.0 + 300, 'D'),
                ('C', 'M') => (res.0 + 800, 'M'),
                (_, 'I') => (res.0 + 1, 'I'),
                (_, 'V') => (res.0 + 5, 'V'),
                (_, 'X') => (res.0 + 10, 'X'),
                (_, 'L') => (res.0 + 50, 'L'),
                (_, 'C') => (res.0 + 100, 'C'),
                (_, 'D') => (res.0 + 500, 'D'),
                (_, 'M') => (res.0 + 1000, 'M'),
                (_, _) => unreachable!(),
            }
        }).0
    }
}
```