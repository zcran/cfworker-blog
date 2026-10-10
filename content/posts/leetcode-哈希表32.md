---
title: "leetcode-哈希表32"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 字母板上的路径

我们从一块字母板上的位置 (0, 0) 出发，该坐标对应的字符为 board[0][0]。

在本题里，字母板为board = ["abcde", "fghij", "klmno", "pqrst", "uvwxy", "z"]。

我们可以按下面的指令规则行动：

如果方格存在，'U' 意味着将我们的位置上移一行；
如果方格存在，'D' 意味着将我们的位置下移一行；
如果方格存在，'L' 意味着将我们的位置左移一列；
如果方格存在，'R' 意味着将我们的位置右移一列；
'!' 会把在我们当前位置 (r, c) 的字符 board[r][c] 添加到答案中。
（注意，字母板上只存在有字母的位置。）

返回指令序列，用最小的行动次数让答案和目标 target 相同。你可以返回任何达成目标的路径。


```
impl Solution {
    pub fn alphabet_board_path(target: String) -> String {
        // 字母板坐标：a..y 为 (i/5, i%5)，'z' 为 (5, 0)。
        let pos = |c: u8| -> (i32, i32) {
            let i = (c - b'a') as i32;
            if i == 25 { (5, 0) } else { (i / 5, i % 5) }
        };

        let mut res = String::new();
        let (mut x, mut y) = (0, 0);

        for c in target.bytes() {
            let (tx, ty) = pos(c);

            if (x, y) == (5, 0) {
                // 当前在 'z'：必须先竖直离开第 5 行，再水平移动。
                let v = if tx > x { 'D' } else { 'U' };
                res.extend(std::iter::repeat(v).take((tx - x).abs() as usize));
                let h = if ty > y { 'R' } else { 'L' };
                res.extend(std::iter::repeat(h).take((ty - y).abs() as usize));
            } else if (tx, ty) == (5, 0) {
                // 目标是 'z'：必须先水平到第 0 列，再竖直下到第 5 行。
                // 为避免经过不存在的 (5, y>0)，先下到第 4 行，水平到第 0 列，再下 1 行。
                res.extend(std::iter::repeat('D').take((4 - x) as usize));
                res.extend(std::iter::repeat('L').take(y as usize));
                res.push('D');
            } else {
                // 普通情况：先水平后竖直，经过的格子均有效。
                let h = if ty > y { 'R' } else { 'L' };
                res.extend(std::iter::repeat(h).take((ty - y).abs() as usize));
                let v = if tx > x { 'D' } else { 'U' };
                res.extend(std::iter::repeat(v).take((tx - x).abs() as usize));
            }

            res.push('!');
            x = tx;
            y = ty;
        }

        res
    }
}
```
