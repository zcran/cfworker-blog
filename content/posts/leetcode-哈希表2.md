---
title: "leetcode-哈希表2"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 罗马数字转整数

罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。

字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000

例如， 罗马数字 2 写做 II ，即为两个并列的 1 。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。
C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。

给定一个罗马数字，将其转换成整数。


```
impl Solution {
    pub fn roman_to_int(s: String) -> i32 {
        // 直接用 match 取值，避免 HashMap 的哈希开销与堆分配。
        #[inline]
        fn val(c: u8) -> i32 {
            match c {
                b'I' => 1,
                b'V' => 5,
                b'X' => 10,
                b'L' => 50,
                b'C' => 100,
                b'D' => 500,
                b'M' => 1000,
                _ => 0,
            }
        }

        let bytes = s.as_bytes();
        let mut ans = 0;
        // 从左到右扫描：若当前字符小于右侧字符，则为减法形式，累加负值；否则累加正值。
        for i in 0..bytes.len() - 1 {
            let cur = val(bytes[i]);
            ans += if cur < val(bytes[i + 1]) { -cur } else { cur };
        }
        // 最后一个字符一定是正值，直接加上。
        ans + val(bytes[bytes.len() - 1])
    }
}
```
