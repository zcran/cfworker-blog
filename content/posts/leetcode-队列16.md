---
title: "leetcode-队列16"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 最大化城市的最小电量


给你一个下标从 0 开始长度为 n 的整数数组 stations ，其中 stations[i] 表示第 i 座城市的供电站数目。

每个供电站可以在一定 范围 内给所有城市提供电力。换句话说，如果给定的范围是 r ，在城市 i 处的供电站可以给所有满足 |i - j| <= r 且 0 <= i, j <= n - 1 的城市 j 供电。

|x| 表示 x 的 绝对值 。比方说，|7 - 5| = 2 ，|3 - 10| = 7 。
一座城市的 电量 是所有能给它供电的供电站数目。

政府批准了可以额外建造 k 座供电站，你需要决定这些供电站分别应该建在哪里，这些供电站与已经存在的供电站有相同的供电范围。

给你两个整数 r 和 k ，如果以最优策略建造额外的发电站，返回所有城市中，最小电量的最大值是多少。

这 k 座供电站可以建在多个城市。


```
impl Solution {
    /// 最大化最小供电功率
    ///
    /// # 思路
    /// 1. 对答案进行二分查找，范围在 `[最小初始覆盖和, 总电力 + k]`
    /// 2. 检查函数：贪心从左到右，不足则增加当前站点电力，并更新后续区间
    /// 3. 使用差分数组快速计算覆盖和
    pub fn max_power(stations: Vec<i32>, r: i32, k: i32) -> i64 {
        let n = stations.len();
        let r = r as usize;
        let k = k as i64;

        // 差分数组构建初始覆盖和
        let mut diff = vec![0i64; n + 1];
        for i in 0..n {
            let left = i.saturating_sub(r);
            let right = (i + r + 1).min(n);
            diff[left] += stations[i] as i64;
            diff[right] -= stations[i] as i64;
        }

        // 计算初始覆盖和数组（前缀和）
        let mut power = vec![0i64; n];
        let mut sum = 0i64;
        for i in 0..n {
            sum += diff[i];
            power[i] = sum;
        }

        let min_val = *power.iter().min().unwrap();
        let total_power = power.iter().sum::<i64>();
        let mut lo = min_val;
        let mut hi = total_power + k;      // 可能的最大值
        let mut best = min_val;

        while lo <= hi {
            let mid = lo + (hi - lo) / 2;
            if Self::can_achieve(&power, r, k, mid) {
                best = mid;
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }
        best
    }

    /// 检查是否能使得所有城市的最小供电 ≥ target
    fn can_achieve(initial: &[i64], r: usize, k: i64, target: i64) -> bool {
        let n = initial.len();
        let mut diff = vec![0i64; n + 1];     // 差分数组记录额外增加的电力
        let mut sum = 0i64;                  // 当前点实际供电（初始+额外）
        let mut used = 0i64;                 // 已使用的额外电力

        for i in 0..n {
            sum += diff[i];                  // 加入额外影响
            let current = initial[i] + sum;  // 当前城市实际供电
            if current < target {
                let need = target - current;
                if used + need > k { return false; }
                used += need;
                // 在 [i, i + 2r] 范围内增加 need
                let end = (i + 2 * r + 1).min(n);
                diff[i] += need;
                diff[end] -= need;
                sum += need;                 // 当前点已经补齐
            }
        }
        true
    }
}
```
