---
title: "leetcode-滑动窗口106"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 收集连续 K 个袋子可以获得的最多硬币数量

在一条数轴上有无限多个袋子，每个坐标对应一个袋子。其中一些袋子里装有硬币。

给你一个二维数组 coins，其中 coins[i] = [li, ri, ci] 表示从坐标 li 到 ri 的每个袋子中都有 ci 枚硬币。

数组 coins 中的区间互不重叠。

另给你一个整数 k。

返回通过收集连续 k 个袋子可以获得的 最多 硬币数量。


```
impl Solution {
    pub fn maximum_coins(mut coins: Vec<Vec<i32>>, k: i32) -> i64 {
        // 按区间左端点排序
        coins.sort_by_key(|c| c[0]);
        let k = k as i64;

        // 计算从左向右覆盖的最大值
        let ans1 = Self::max_cover(&coins, k);

        // 反转坐标，计算从右向左覆盖的最大值（等价于在镜像上从左向右）
        for c in &mut coins {
            let l = c[0];
            let r = c[1];
            c[0] = -r;
            c[1] = -l;
        }
        coins.sort_by_key(|c| c[0]);
        let ans2 = Self::max_cover(&coins, k);

        ans1.max(ans2)
    }

    /// 计算毯子覆盖的最大硬币数（所有区间已按左端点排序）
    fn max_cover(coins: &[Vec<i32>], k: i64) -> i64 {
        let mut ans = 0i64;
        let mut cover = 0i64;    // 当前窗口覆盖的总硬币数
        let mut left = 0usize;   // 窗口左边界对应的区间索引

        for right in 0..coins.len() {
            let r_l = coins[right][0] as i64;
            let r_r = coins[right][1] as i64;
            let r_c = coins[right][2] as i64;
            cover += (r_r - r_l + 1) * r_c;

            // 窗口右端固定为当前区间的右端点，计算窗口左端点位置
            let window_left = r_r - k + 1;

            // 移除完全在窗口左侧的区间
            while (coins[left][1] as i64) < window_left {
                let l_l = coins[left][0] as i64;
                let l_r = coins[left][1] as i64;
                let l_c = coins[left][2] as i64;
                cover -= (l_r - l_l + 1) * l_c;
                left += 1;
            }

            // 减去窗口左侧未覆盖的部分（部分在窗口内的区间）
            let uncover = ((window_left - coins[left][0] as i64).max(0)) * (coins[left][2] as i64);
            ans = ans.max(cover - uncover);
        }

        ans
    }
}
```
