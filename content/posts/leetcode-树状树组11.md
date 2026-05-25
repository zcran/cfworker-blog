---
title: "leetcode-树状树组11"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---



## 最多 K 次交换相邻数位后得到的最小整数

给你一个字符串 num 和一个整数 k 。其中，num 表示一个很大的整数，字符串中的每个字符依次对应整数上的各个 数位 。

你可以交换这个整数相邻数位的数字 最多 k 次。

请你返回你能得到的最小整数，并以字符串形式返回。

```
use std::cell::RefCell;

struct Fenwick {
    tree: RefCell<Vec<i32>>,
    size: usize,
}

impl Fenwick {
    fn new(size: usize) -> Self {
        Fenwick {
            tree: RefCell::new(vec![0; size + 1]),
            size,
        }
    }

    fn update(&self, index: usize, delta: i32) {
        let mut idx = index as i32;
        let mut tree = self.tree.borrow_mut();
        while idx <= self.size as i32 {
            tree[idx as usize] += delta;
            idx += idx & -idx;
        }
    }

    fn prefix_sum(&self, idx: i32) -> i32 {
        let mut i = idx;
        let mut sum = 0;
        let tree = self.tree.borrow();
        while i > 0 {
            sum += tree[i as usize];
            i -= i & -i;
        }
        sum
    }

    fn range_sum(&self, l: i32, r: i32) -> i32 {
        self.prefix_sum(r) - self.prefix_sum(l - 1)
    }
}

impl Solution {
    pub fn min_integer(num: String, k: i32) -> String {
        let bytes = num.as_bytes();
        let n = bytes.len();

        let mut positions: Vec<Vec<usize>> = vec![Vec::new(); 10];
        for (idx, &ch) in bytes.iter().enumerate().rev() {
            let d = (ch - b'0') as usize;
            positions[d].push(idx + 1);
        }

        let bit = Fenwick::new(n);
        let mut result = vec![0u8; n];
        let mut result_idx = 0;
        let mut k_rem = k;

        for i in 1..=n {
            for digit in 0..10 {
                if let Some(&pos) = positions[digit].last() {
                    let right_removed = bit.range_sum(pos as i32, n as i32);
                    let dist = pos as i32 + right_removed - i as i32;
                    if dist <= k_rem {
                        bit.update(pos, 1);
                        positions[digit].pop();
                        result[result_idx] = b'0' + digit as u8;
                        result_idx += 1;
                        k_rem -= dist;
                        break;
                    }
                }
            }
        }

        unsafe { String::from_utf8_unchecked(result) }
    }
}
```
