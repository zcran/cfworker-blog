---
title: "leetcode-贪心33"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 行相等的最少多米诺旋转

在一排多米诺骨牌中，tops[i] 和 bottoms[i] 分别代表第 i 个多米诺骨牌的上半部分和下半部分。（一个多米诺是两个从 1 到 6 的数字同列平铺形成的 —— 该平铺的每一半上都有一个数字。）

我们可以旋转第 i 张多米诺，使得 tops[i] 和 bottoms[i] 的值交换。

返回能使 tops 中所有值或者 bottoms 中所有值都相同的最小旋转次数。

如果无法做到，返回 -1.


```
impl Solution {
    pub fn min_domino_rotations(tops: Vec<i32>, bottoms: Vec<i32>) -> i32 {
        // 若存在解，最终统一的值必然是第一张牌的某一面：tops[0] 或 bottoms[0]
        fn check(x: i32, tops: &[i32], bottoms: &[i32]) -> i32 {
            // rot_top: 把 x 都转到上面所需的旋转次数
            // rot_bot: 把 x 都转到下面所需的旋转次数
            let (mut rot_top, mut rot_bot) = (0, 0);

            for (&t, &b) in tops.iter().zip(bottoms) {
                // 某一面上既无 x，则无法让 x 统一出现在同一面
                if t != x && b != x {
                    return -1;
                } else if t != x {
                    rot_top += 1;   // x 在下面，需旋转到上面
                } else if b != x {
                    rot_bot += 1;   // x 在上面，需旋转到下面
                }
            }

            rot_top.min(rot_bot)
        }

        let ans = check(tops[0], &tops, &bottoms);
        // 第一张两面相同则无需再检查另一面
        if ans != -1 || tops[0] == bottoms[0] {
            return ans;
        }
        check(bottoms[0], &tops, &bottoms)
    }
}
```
