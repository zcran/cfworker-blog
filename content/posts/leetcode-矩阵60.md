---
title: "leetcode-矩阵60"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 找出网格的区域平均强度

给你一个下标从 0 开始、大小为 m x n 的网格 image ，表示一个灰度图像，其中 image[i][j] 表示在范围 [0..255] 内的某个像素强度。另给你一个 非负 整数 threshold 。

如果 image[a][b] 和 image[c][d] 满足 |a - c| + |b - d| == 1 ，则称这两个像素是 相邻像素 。

区域 是一个 3 x 3 的子网格，且满足区域中任意两个 相邻 像素之间，像素强度的 绝对差 小于或等于 threshold 。

区域 内的所有像素都认为属于该区域，而一个像素 可以 属于 多个 区域。

你需要计算一个下标从 0 开始、大小为 m x n 的网格 result ，其中 result[i][j] 是 image[i][j] 所属区域的 平均 强度，向下取整 到最接近的整数。如果 image[i][j] 属于多个区域，result[i][j] 是这些区域的 “取整后的平均强度” 的 平均值，也 向下取整 到最接近的整数。如果 image[i][j] 不属于任何区域，则 result[i][j] 等于 image[i][j] 。

返回网格 result 。


```
impl Solution {
    /// 判断以 (i,j) 为中心的 3×3 区域是否合法：
    /// 只需检查中心与 8 邻格、以及外圈相邻格两两之间的强度差 ≤ threshold。
    /// 由于相邻关系只涉及边相邻，检查以下 12 对即可覆盖全部 12 条边：
    ///   中心与上下左右 4 对，外圈四边各 2 对（共 8 对）。
    ///
    /// 对每个合法区域，将其平均值累加到 9 个格子；最后按参与次数取平均。
    /// 不合法或未参与的格子保持原值。
    ///
    /// 时间复杂度 O(m*n)，空间 O(m*n)。
    pub fn result_grid(image: Vec<Vec<i32>>, threshold: i32) -> Vec<Vec<i32>> {
        let m = image.len();
        let n = image[0].len();
        let mut ans = image.clone();
        // cnt[i][j]：格子 (i,j) 参与的区域数；0 表示未参与任何区域
        let mut cnt = vec![vec![0i32; n]; m];

        // 仅中心点在 [1, m-2] × [1, n-2] 时才可能构成完整 3×3 区域
        for i in 1..m - 1 {
            for j in 1..n - 1 {
                // 检查 12 条相邻边是否都满足阈值
                let ok = (image[i][j] - image[i][j + 1]).abs() <= threshold
                    && (image[i][j] - image[i][j - 1]).abs() <= threshold
                    && (image[i][j] - image[i - 1][j]).abs() <= threshold
                    && (image[i][j] - image[i + 1][j]).abs() <= threshold
                    // 上边两格
                    && (image[i - 1][j - 1] - image[i - 1][j]).abs() <= threshold
                    && (image[i - 1][j] - image[i - 1][j + 1]).abs() <= threshold
                    // 下边两格
                    && (image[i + 1][j - 1] - image[i + 1][j]).abs() <= threshold
                    && (image[i + 1][j] - image[i + 1][j + 1]).abs() <= threshold
                    // 左边两格
                    && (image[i - 1][j - 1] - image[i][j - 1]).abs() <= threshold
                    && (image[i][j - 1] - image[i + 1][j - 1]).abs() <= threshold
                    // 右边两格
                    && (image[i - 1][j + 1] - image[i][j + 1]).abs() <= threshold
                    && (image[i][j + 1] - image[i + 1][j + 1]).abs() <= threshold;

                if !ok {
                    continue;
                }

                // 区域平均强度（向下取整）
                let mut sum = 0;
                for ii in i - 1..=i + 1 {
                    for jj in j - 1..=j + 1 {
                        sum += image[ii][jj];
                    }
                }
                let avg = sum / 9;

                // 累加到 9 个格子
                for ii in i - 1..=i + 1 {
                    for jj in j - 1..=j + 1 {
                        if cnt[ii][jj] == 0 {
                            ans[ii][jj] = avg;
                        } else {
                            ans[ii][jj] += avg;
                        }
                        cnt[ii][jj] += 1;
                    }
                }
            }
        }

        // 对参与过区域的格子取平均（向下取整）
        for i in 0..m {
            for j in 0..n {
                if cnt[i][j] > 0 {
                    ans[i][j] /= cnt[i][j];
                }
            }
        }

        ans
    }
}
```
