---
title: "leetcode-博弈18"
date: 2026-04-09T20:51:31+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 无限棋局

小力正在通过残局练习来备战「力扣挑战赛」中的「五子棋」项目，他想请你能帮他预测当前残局的输赢情况。棋盘中的棋子分布信息记录于二维数组 pieces 中，其中 pieces[i] = [x,y,color] 表示第 i 枚棋子的横坐标为 x，纵坐标为 y，棋子颜色为 color(0 表示黑棋，1 表示白棋)。假如黑棋先行，并且黑棋和白棋都按最优策略落子，请你求出当前棋局在三步（按 黑、白、黑 的落子顺序）之内的输赢情况（三步之内先构成同行、列或对角线连续同颜色的至少 5 颗即为获胜）：

黑棋胜, 请返回 "Black"
白棋胜, 请返回 "White"
仍无胜者, 请返回 "None"
注意：

和传统的五子棋项目不同，「力扣挑战赛」中的「五子棋」项目 不存在边界限制，即可在 任意位置 落子；
黑棋和白棋均按 3 步内的输赢情况进行最优策略的选择
测试数据保证所给棋局目前无胜者；
测试数据保证不会存在坐标一样的棋子。


```
use std::collections::{BTreeSet, HashMap, HashSet};

impl Solution {
    /// 五子棋决胜判断（简化版）
    /// 返回 "Black", "White" 或 "None"
    pub fn gobang(pieces: Vec<Vec<i32>>) -> String {
        // 四个方向：0水平，1垂直，2主对角线（y-x），3副对角线（y+x）
        // 每个方向用一个 HashMap 存储：key -> 有序集合（存储对应的坐标分量）
        let mut black = [HashMap::new(), HashMap::new(), HashMap::new(), HashMap::new()];
        let mut white = [HashMap::new(), HashMap::new(), HashMap::new(), HashMap::new()];

        // 向指定颜色的某个方向添加棋子（独立函数，不捕获环境）
        fn add_piece(
            color: &mut [HashMap<i32, BTreeSet<i32>>; 4],
            x: i32,
            y: i32,
        ) {
            // 水平方向：key = y, value = x 集合
            color[0].entry(y).or_insert_with(BTreeSet::new).insert(x);
            // 垂直方向：key = x, value = y 集合
            color[1].entry(x).or_insert_with(BTreeSet::new).insert(y);
            // 主对角线方向：key = y - x, value = x 集合
            color[2].entry(y - x).or_insert_with(BTreeSet::new).insert(x);
            // 副对角线方向：key = y + x, value = x 集合
            color[3].entry(y + x).or_insert_with(BTreeSet::new).insert(x);
        }

        // 判断某个位置是否有对方的棋子（用于阻挡检查）
        fn has_piece(
            color: &[HashMap<i32, BTreeSet<i32>>; 4],
            d: usize,
            k: i32,
            v: i32,
        ) -> bool {
            color[d].get(&k).map_or(false, |set| set.contains(&v))
        }

        // 从 (k, v, d) 还原实际棋盘坐标 (x, y)
        fn get_pos(d: usize, k: i32, v: i32) -> (i32, i32) {
            match d {
                0 => (v, k),      // 水平: x = v, y = k
                1 => (k, v),      // 垂直: x = k, y = v
                2 => (v, k + v),  // 主对角线: x = v, y = k + v
                3 => (v, k - v),  // 副对角线: x = v, y = k - v
                _ => unreachable!(),
            }
        }

        // 核心查找函数：在颜色 P 中寻找能形成 np 连（3或4）且不被对方 Q 阻挡的获胜点
        // 返回值: HashMap<(x,y), HashSet<(x2,y2)>>，键为填补点，值为与该点配对的另一个填补点（双三/双四需要）
        fn find_win_points(
            p: &[HashMap<i32, BTreeSet<i32>>; 4],
            q: &[HashMap<i32, BTreeSet<i32>>; 4],
            np: usize,
        ) -> HashMap<(i32, i32), HashSet<(i32, i32)>> {
            let mut win = HashMap::new();
            for d in 0..4 {
                for (k, set) in &p[d] {
                    let n = set.len();
                    if n < np {
                        continue;
                    }
                    // 将 BTreeSet 转为 Vec 以便按索引访问
                    let xs: Vec<i32> = set.iter().copied().collect();
                    for i in 0..=(n - np) {
                        let left = xs[i];
                        let right = xs[i + np - 1];
                        let diff = right - left; // 跨度
                        if diff < 5 {
                            // 可能形成五连的候选空缺位置范围 [left + diff - 4, left + 5)
                            let start = left + diff - 4;
                            let end = left + 5;
                            // 收集所有可能的空缺位置（x坐标）
                            let candidates: Vec<i32> = (start..end)
                                .filter(|&v| {
                                    // 空缺位置不能在已有棋子中
                                    !xs[i..i + np].contains(&v) &&
                                    // 空缺位置不能有对方棋子阻挡
                                    !has_piece(q, d, *k, v)
                                })
                                .collect();
                            let need = 5 - np; // 3连需要2个空位，4连需要1个空位
                            if candidates.len() < need {
                                continue;
                            }
                            // 滑动窗口检查连续的空位段
                            for j in 0..=(candidates.len() - need) {
                                let c1 = candidates[j];
                                let c2 = candidates[j + need - 1];
                                if c2 - c1 > 4 {
                                    continue;
                                }
                                let pos1 = get_pos(d, *k, c1);
                                let pos2 = get_pos(d, *k, c2);
                                win.entry(pos1).or_insert_with(HashSet::new).insert(pos2);
                                win.entry(pos2).or_insert_with(HashSet::new).insert(pos1);
                                // 如果发现两个以上的获胜点（针对冲4），可以提前返回
                                if np == 4 && win.len() > 1 {
                                    return win;
                                }
                            }
                        }
                    }
                }
            }
            win
        }

        // 1. 录入所有棋子
        for piece in &pieces {
            let x = piece[0];
            let y = piece[1];
            let c = piece[2]; // 0 黑，1 白
            if c == 0 {
                add_piece(&mut black, x, y);
            } else {
                add_piece(&mut white, x, y);
            }
        }

        // 2. 黑先手：如果有冲4获胜点，则黑胜
        let black_win4 = find_win_points(&black, &white, 4);
        if !black_win4.is_empty() {
            return "Black".to_string();
        }

        // 3. 检查白方的冲4
        let white_win4 = find_win_points(&white, &black, 4);
        if white_win4.len() > 1 {
            return "White".to_string();
        }

        // 4. 只有一个白方冲4，黑方必须堵上
        if white_win4.len() == 1 {
            let &(x, y) = white_win4.keys().next().unwrap();
            // 黑棋落子堵住
            add_piece(&mut black, x, y);
            let new_black_win4 = find_win_points(&black, &white, 4);
            if new_black_win4.len() > 1 {
                return "Black".to_string();
            } else {
                return "None".to_string();
            }
        }

        // 5. 检查是否有双三（即冲3获胜点中有多个配对的）
        let black_win3 = find_win_points(&black, &white, 3);
        let has_double_three = black_win3.values().any(|set| set.len() > 1);
        if has_double_three {
            return "Black".to_string();
        }
        "None".to_string()
    }
}
```
