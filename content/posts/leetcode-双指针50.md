---
title: "leetcode-双指针50"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 推多米诺

n 张多米诺骨牌排成一行，将每张多米诺骨牌垂直竖立。在开始时，同时把一些多米诺骨牌向左或向右推。

每过一秒，倒向左边的多米诺骨牌会推动其左侧相邻的多米诺骨牌。同样地，倒向右边的多米诺骨牌也会推动竖立在其右侧的相邻多米诺骨牌。

如果一张垂直竖立的多米诺骨牌的两侧同时有多米诺骨牌倒下时，由于受力平衡， 该骨牌仍然保持不变。

就这个问题而言，我们会认为一张正在倒下的多米诺骨牌不会对其它正在倒下或已经倒下的多米诺骨牌施加额外的力。

给你一个字符串 dominoes 表示这一行多米诺骨牌的初始状态，其中：

dominoes[i] = 'L'，表示第 i 张多米诺骨牌被推向左侧，
dominoes[i] = 'R'，表示第 i 张多米诺骨牌被推向右侧，
dominoes[i] = '.'，表示没有推动第 i 张多米诺骨牌。

返回表示最终状态的字符串。


```
impl Solution {
    pub fn push_dominoes(dominoes: String) -> String {
        let mut chars: Vec<char> = dominoes.chars().collect();
        let n = chars.len();

        // 从左向右扫描，记录每个位置受到的向右推力
        let mut force = vec![0; n];
        let mut cur_force = 0;
        for i in 0..n {
            if chars[i] == 'R' {
                cur_force = n; // 重置推力为最大值
            } else if chars[i] == 'L' {
                cur_force = 0; // 被左推挡住
            } else if cur_force > 0 {
                cur_force -= 1; // 向右推力逐渐减弱
            }
            force[i] = cur_force;
        }

        // 从右向左扫描，叠加向左推力
        cur_force = 0;
        for i in (0..n).rev() {
            if chars[i] == 'L' {
                cur_force = n;
            } else if chars[i] == 'R' {
                cur_force = 0;
            } else if cur_force > 0 {
                cur_force -= 1;
            }
            // 比较左右推力决定最终状态
            if chars[i] == '.' {
                if force[i] > cur_force {
                    chars[i] = 'R';
                } else if force[i] < cur_force {
                    chars[i] = 'L';
                } // 相等则保持 '.'
            }
        }

        chars.into_iter().collect()
    }
}
```
