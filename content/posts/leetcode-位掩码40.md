---
title: "leetcode-位掩码40"
date: 2026-05-18T10:28:00+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 守护太空城

各位勇者请注意，力扣太空城发布陨石雨红色预警。

太空城中的一些舱室将要受到陨石雨的冲击，这些舱室按照编号 0 ~ N 的顺序依次排列。为了阻挡陨石损毁舱室，太空城可以使用能量展开防护屏障，具体消耗如下：

· 选择一个舱室开启屏障，能量消耗为 2
· 选择相邻两个舱室开启联合屏障，能量消耗为 3
· 对于已开启的一个屏障，多维持一时刻，能量消耗为 1

已知陨石雨的影响范围和到达时刻，time[i] 和 position[i] 分别表示该陨石的到达时刻和冲击位置。请返回太空舱能够守护所有舱室所需要的最少能量。

注意：

同一时间，一个舱室不能被多个屏障覆盖
陨石雨仅在到达时刻对冲击位置处的舱室有影响

```
impl Solution {
    pub fn defend_space_city(time: Vec<i32>, position: Vec<i32>) -> i32 {
        // 1. 获取最大位置和最大时间
        let n = *position.iter().max().unwrap() as usize;
        let max_t = *time.iter().max().unwrap() as usize;
        let m = 1 << max_t; // 时间状态掩码总数

        // 2. 构建每个位置下雨的时间掩码
        let mut rain = vec![0u32; n + 1];
        for (&t, &p) in time.iter().zip(position.iter()) {
            let pos = p as usize;
            rain[pos] |= 1 << (t as u32 - 1);
        }

        // 3. 预处理 un 和 single 数组（代价表）
        let mut un = vec![0u32; m];
        let mut single = vec![0u32; m];
        for i in 1..m {
            let lb = i & i.wrapping_neg();      // 最低位 1
            let j = i ^ lb;                     // 去掉最低位
            let lb2 = j & j.wrapping_neg();     // j 的最低位（j=0 时为 0）
            let adjacent = (lb == (lb2 >> 1)) as usize; // 0 或 1
            un[i] = un[j] + if adjacent == 1 { 1 } else { 3 };
            single[i] = single[j] + if adjacent == 1 { 1 } else { 2 };
        }

        // 4. DP 滚动数组：prev[mask], cur[mask]
        let full_mask = (m - 1) as u32;
        let inf = i32::MAX;
        let mut prev = vec![inf; m];
        // 初始化 f[0][j] = un[j] + single[ (full_mask ^ j) & rain[0] ]
        for j in 0..m {
            let j_mask = j as u32;
            let comp = (full_mask ^ j_mask) & rain[0];
            let cost = (un[j] + single[comp as usize]) as i32;
            prev[j] = cost;
        }

        // 逐位置递推
        for i in 1..=n {
            let mut cur = vec![inf; m];
            for j in 0..m {
                let j_mask = j as u32;
                let mask = full_mask ^ j_mask; // 当前行 j 的补集
                // 枚举 mask 的所有子集 pre
                let mut sub = mask;
                loop {
                    let pre = sub as usize;
                    let prev_val = prev[pre];
                    if prev_val != inf {
                        let rest = (mask ^ sub) & rain[i];
                        let cost = prev_val + (un[j] + single[rest as usize]) as i32;
                        if cost < cur[j] {
                            cur[j] = cost;
                        }
                    }
                    if sub == 0 { break; }
                    sub = (sub - 1) & mask;
                }
            }
            prev = cur;
        }

        // 最终结果：f[n][0]
        prev[0]
    }
}
```
