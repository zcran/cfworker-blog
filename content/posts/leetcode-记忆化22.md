---
title: "leetcode-记忆化22"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 最大化网格幸福感


给你四个整数 m、n、introvertsCount 和 extrovertsCount 。有一个 m x n 网格，和两种类型的人：内向的人和外向的人。总共有 introvertsCount 个内向的人和 extrovertsCount 个外向的人。

请你决定网格中应当居住多少人，并为每个人分配一个网格单元。 注意，不必 让所有人都生活在网格中。

每个人的 幸福感 计算如下：

内向的人 开始 时有 120 个幸福感，但每存在一个邻居（内向的或外向的）他都会 失去  30 个幸福感。
外向的人 开始 时有 40 个幸福感，每存在一个邻居（内向的或外向的）他都会 得到  20 个幸福感。

邻居是指居住在一个人所在单元的上、下、左、右四个直接相邻的单元中的其他人。

网格幸福感 是每个人幸福感的 总和 。 返回 最大可能的网格幸福感 。

```
impl Solution {
    /// 计算网格最大幸福感（LeetCode 1659）
    /// 参数：
    /// - m：行数（1 ≤ m ≤ 5）
    /// - n：列数（1 ≤ n ≤ 5）
    /// - introverts_count：内向者人数（0 ≤ count ≤ 6）
    /// - extroverts_count：外向者人数（0 ≤ count ≤ 6）
    /// 返回：最大幸福值
    pub fn get_max_grid_happiness(
        m: i32,
        n: i32,
        introverts_count: i32,
        extroverts_count: i32,
    ) -> i32 {
        let rows = m as usize;
        let cols = n as usize;
        let max_intro = introverts_count as usize;
        let max_extro = extroverts_count as usize;

        // 每行所有可能的状态（三进制编码，0:空,1:内向,2:外向）
        let states_count = 3_usize.pow(cols as u32); // 最多 3^5 = 243
        // 预先计算每个状态包含的内向人数和外向人数
        let state_counts = (0..states_count)
            .map(|state| Self::count_people(state, cols))
            .collect::<Vec<_>>(); // 每个元素为 (intro_count, extro_count)

        // 预先计算任意两个相邻行之间的得分（上一行状态 -> 当前行状态）
        let transition_scores = (0..states_count)
            .map(|prev| {
                (0..states_count)
                    .map(|curr| Self::compute_row_score(prev, curr, cols))
                    .collect::<Vec<_>>()
            })
            .collect::<Vec<_>>();

        // DP 表：
        // dp[row][used_i][used_e][state] = 最大幸福感
        // 维度：行数+1，内向人数+1，外向人数+1，状态数
        let mut dp = vec![
            vec![
                vec![vec![i32::MIN; states_count]; max_extro + 1];
                max_intro + 1
            ];
            rows + 1
        ];

        dp[0][0][0][0] = 0; // 初始状态：0行，0人，状态0（全空）

        let mut ans = 0;

        // 逐行 DP
        for row in 1..=rows {
            for used_i in 0..=max_intro {
                for used_e in 0..=max_extro {
                    for curr_state in 0..states_count {
                        let (intro_in_curr, extro_in_curr) = state_counts[curr_state];
                        // 当前行放置的人数不能超过剩余名额
                        if intro_in_curr > used_i || extro_in_curr > used_e {
                            continue;
                        }

                        // 尝试所有上一行状态
                        for prev_state in 0..states_count {
                            let prev_val = dp[row - 1][used_i - intro_in_curr][used_e - extro_in_curr][prev_state];
                            if prev_val == i32::MIN {
                                continue; // 上一行状态不可达
                            }
                            let gain = transition_scores[prev_state][curr_state];
                            let new_val = prev_val + gain;
                            let cell = &mut dp[row][used_i][used_e][curr_state];
                            if new_val > *cell {
                                *cell = new_val;
                            }
                        }

                        // 更新答案（最后一行）
                        if row == rows {
                            ans = ans.max(dp[row][used_i][used_e][curr_state]);
                        }
                    }
                }
            }
        }

        ans
    }

    /// 统计一个状态中包含的内向者数量（1）和外向者数量（2）
    /// 参数：
    /// - state：三进制编码的状态，最低位对应最左列
    /// - cols：列数
    /// 返回：(内向人数, 外向人数)
    fn count_people(state: usize, cols: usize) -> (usize, usize) {
        let mut intro = 0;
        let mut extro = 0;
        let mut s = state;
        for _ in 0..cols {
            match s % 3 {
                1 => intro += 1,
                2 => extro += 1,
                _ => {}
            }
            s /= 3;
        }
        (intro, extro)
    }

    /// 计算上一行状态 prev 与当前行状态 curr 相邻时，当前行贡献的幸福感增量。
    /// 规则依据 LeetCode 1659 官方题解：
    /// - 内向者（1）：基础分 +120；每相邻一个非空（左右或上下）减30；若相邻为内向者再减30，相邻为外向者加20。
    /// - 外向者（2）：基础分 +40；每相邻一个非空（左右或上下）加20；若相邻为内向者减30，相邻为外向者加20。
    /// - 空（0）：无贡献。
    /// 注意：该函数同时考虑垂直和水平相邻，并采用逐个扫描的方式计算。
    fn compute_row_score(prev: usize, curr: usize, cols: usize) -> i32 {
        // 将状态分解为列数组（最低位为第0列）
        let mut prev_arr = [0u8; 5];
        let mut curr_arr = [0u8; 5];
        let mut p = prev;
        let mut c = curr;
        for i in 0..cols {
            prev_arr[i] = (p % 3) as u8;
            curr_arr[i] = (c % 3) as u8;
            p /= 3;
            c /= 3;
        }

        let mut score = 0;
        for i in 0..cols {
            match curr_arr[i] {
                1 => {
                    // 内向者基础分
                    score += 120;
                    // 左邻
                    if i > 0 && curr_arr[i - 1] != 0 {
                        score -= 30;
                    }
                    // 右邻
                    if i + 1 < cols && curr_arr[i + 1] != 0 {
                        score -= 30;
                    }
                    // 上邻
                    if prev_arr[i] != 0 {
                        score -= 30;
                    }
                    // 特定相邻类型额外调整
                    match prev_arr[i] {
                        1 => score -= 30,
                        2 => score += 20,
                        _ => {}
                    }
                }
                2 => {
                    // 外向者基础分
                    score += 40;
                    if i > 0 && curr_arr[i - 1] != 0 {
                        score += 20;
                    }
                    if i + 1 < cols && curr_arr[i + 1] != 0 {
                        score += 20;
                    }
                    if prev_arr[i] != 0 {
                        score += 20;
                    }
                    match prev_arr[i] {
                        1 => score -= 30,
                        2 => score += 20,
                        _ => {}
                    }
                }
                _ => {} // 空格无贡献
            }
        }
        score
    }
}
```
