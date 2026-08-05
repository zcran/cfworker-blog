---
title: "leetcode-回溯58"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 基于陈述统计最多好人数

游戏中存在两种角色：

· 好人：该角色只说真话。
· 坏人：该角色可能说真话，也可能说假话。

给你一个下标从 0 开始的二维整数数组 statements ，大小为 n x n ，表示 n 个玩家对彼此角色的陈述。具体来说，statements[i][j] 可以是下述值之一：

· 0 表示 i 的陈述认为 j 是 坏人 。
· 1 表示 i 的陈述认为 j 是 好人 。
· 2 表示 i 没有对 j 作出陈述。

另外，玩家不会对自己进行陈述。形式上，对所有 0 <= i < n ，都有 statements[i][i] = 2 。

根据这 n 个玩家的陈述，返回可以认为是 好人 的 最大 数目。

```
impl Solution {
    pub fn maximum_good(statements: Vec<Vec<i32>>) -> i32 {
        let n = statements.len();
        let mut ans = 0;

        // 预处理：将每个人的陈述转换为位掩码
        // good_mask[i]: i 认为谁是好人 (位j为1表示i认为j是好人)
        // unknown_mask[i]: i 对谁没有陈述 (位j为1表示i没对j陈述)
        let mut good_mask = vec![0; n];
        let mut unknown_mask = vec![0; n];
        for i in 0..n {
            let mut gm = 0;
            let mut um = 0;
            for j in 0..n {
                match statements[i][j] {
                    1 => gm |= 1 << j,  // 认为是好人
                    2 => um |= 1 << j,  // 没有陈述
                    _ => {}              // 0: 认为是坏人，无需处理
                }
            }
            good_mask[i] = gm;
            unknown_mask[i] = um;
        }

        // 枚举所有可能的身份组合 (0=坏人, 1=好人)
        for mask in 0..(1 << n) {
            let mut good_count = 0;
            let mut valid = true;

            // 检查每个玩家
            for i in 0..n {
                // 如果当前玩家是好人
                if (mask >> i) & 1 == 1 {
                    good_count += 1;

                    // 好人的陈述必须为真：他所声称的好人必须是好人，坏人也必须是坏人
                    // 检查所有陈述（包括认为好人或坏人）
                    for j in 0..n {
                        if statements[i][j] == 1 && (mask >> j) & 1 == 0 {
                            // 声称j是好人，但j实际是坏人 -> 矛盾
                            valid = false;
                            break;
                        }
                        if statements[i][j] == 0 && (mask >> j) & 1 == 1 {
                            // 声称j是坏人，但j实际是好人 -> 矛盾
                            valid = false;
                            break;
                        }
                    }
                }
                // 坏人的陈述无需验证（可能说真话也可能说假话）

                if !valid {
                    break;
                }
            }

            if valid {
                ans = ans.max(good_count);
            }
        }

        ans
    }
}
```
