---
title: "leetcode-脑经急转弯5"
date: 2026-03-24T20:55:57+08:00
tags: ["leetcode", "脑筋急转弯"]
draft: false
---

## 移动石子直到连续

三枚石子放置在数轴上，位置分别为 a，b，c。

每一回合，你可以从两端之一拿起一枚石子（位置最大或最小），并将其放入两端之间的任一空闲位置。形式上，假设这三枚石子当前分别位于位置 x, y, z 且 x < y < z。那么就可以从位置 x 或者是位置 z 拿起一枚石子，并将该石子移动到某一整数位置 k 处，其中 x < k < z 且 k != y。

当你无法进行任何移动时，即，这些石子的位置连续时，游戏结束。

要使游戏结束，你可以执行的最小和最大移动次数分别是多少？ 以长度为 2 的数组形式返回答案：answer = [minimum_moves, maximum_moves]


```
impl Solution {
    pub fn num_moves_stones(a: i32, b: i32, c: i32) -> Vec<i32> {
        let mut places: Vec<i32> = vec![a, b, c];
        places.sort();
        let max_num: i32 = places[2] - places[0] - 2;
        let min_num: i32;
        let mut gaps: Vec<i32> = vec![places[1] - places[0] - 1, places[2] - places[1] - 1];
        gaps.sort();
        min_num = if gaps[0] == 0 {
            if gaps[1] == 0 {
                0
            } else {
                1
            }
        } else if gaps[0] == 1 {
            1
        } else {
            2
        };
        vec![min_num, max_num]
    }
}

```

最大值：所有石子逐渐向中间靠拢，每次移动 1 格，总步数 = 最右与最左石子之间的距离减去 2（因为最终只需占据 3 个位置）
最小值：根据间隙情况：

已连续：0
有一个间隙为 1（或一边紧邻另一边差 2）：1
其他情况：2
