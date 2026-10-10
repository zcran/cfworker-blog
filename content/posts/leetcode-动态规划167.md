---
title: "leetcode-动态规划167"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 恰好 K 个下标对的最大得分

给你两个长度分别为 n 和 m 的整数数组 nums1 和 nums2，以及一个整数 k。

你必须 恰好 选择 k 对下标 (i1, j1), (i2, j2), ..., (ik, jk)，使得：

0 <= i1 < i2 < ... < ik < n
0 <= j1 < j2 < ... < jk < m

对于每对选择的下标 (i, j)，你将获得 nums1[i] * nums2[j] 的得分。

总 得分 是所有选定下标对的乘积的 总和。

返回一个整数，表示可以获得的 最大 总得分。




```
impl Solution {
    pub fn max_score(nums1: Vec<i32>, nums2: Vec<i32>, k: i32) -> i64 {
        let n = nums1.len();
        let m = nums2.len();
        let k = k as usize;
        const NEG: i64 = i64::MIN / 2;

        // f[i][j]：从前 i 个 nums1 和 前 j 个 nums2 中选出 t 对下标的最大得分
        // g 用于滚动更新下一轮 t+1 的状态
        let mut f = vec![vec![NEG; m + 1]; n + 1];
        let mut g = vec![vec![NEG; m + 1]; n + 1];

        // t = 0 时，不选任何对，得分为 0
        for i in 0..=n {
            for j in 0..=m {
                f[i][j] = 0;
            }
        }

        for t in 1..=k {
            // 清空 g 的有效区域；边界处需要保留 NEG，防止非法状态转移
            for row in g.iter_mut() {
                row.iter_mut().for_each(|v| *v = NEG);
            }

            // 至少需要 t 个元素，且后面还要留 k-t 个元素给后续轮次
            for i in t..=n - (k - t) {
                for j in t..=m - (k - t) {
                    // 不选 (i-1, j-1)：从上方或左方继承
                    let best = g[i - 1][j].max(g[i][j - 1]);
                    // 选 (i-1, j-1)：在 f[i-1][j-1] 基础上加上乘积
                    let take = f[i - 1][j - 1] + nums1[i - 1] as i64 * nums2[j - 1] as i64;
                    g[i][j] = best.max(take);
                }
            }

            std::mem::swap(&mut f, &mut g);
        }

        f[n][m]
    }
}
```
