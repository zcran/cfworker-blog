---
title: "leetcode-矩阵16"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 图像重叠

给你两个图像 img1 和 img2 ，两个图像的大小都是 n x n ，用大小相同的二进制正方形矩阵表示。二进制矩阵仅由若干 0 和若干 1 组成。

转换 其中一个图像，将所有的 1 向左，右，上，或下滑动任何数量的单位；然后把它放在另一个图像的上面。该转换的 重叠 是指两个图像 都 具有 1 的位置的数目。

请注意，转换 不包括 向任何方向旋转。越过矩阵边界的 1 都将被清除。

最大可能的重叠数量是多少？


```
impl Solution {
    pub fn largest_overlap(img1: Vec<Vec<i32>>, img2: Vec<Vec<i32>>) -> i32 {
        let n = img1.len();

        // 收集两个图像中所有 1 的坐标
        let mut ones1 = Vec::new();
        let mut ones2 = Vec::new();
        for r in 0..n {
            for c in 0..n {
                if img1[r][c] == 1 {
                    ones1.push((r as i32, c as i32));
                }
                if img2[r][c] == 1 {
                    ones2.push((r as i32, c as i32));
                }
            }
        }

        // 任一图像无 1 时重叠为 0
        if ones1.is_empty() || ones2.is_empty() {
            return 0;
        }

        // 用一个 n*n 的布尔网格做 O(1) 命中判断，比 HashSet 更快更省内存
        let mut has2 = vec![false; n * n];
        for &(r, c) in &ones2 {
            has2[r as usize * n + c as usize] = true;
        }

        // 用一个 n*n 的标记数组记录已枚举过的平移量 (dr, dc)，
        // 平移量范围 [-n+1, n-1]，映射到 [0, 2n-2]
        let size = 2 * n - 1;
        let mut seen = vec![false; size * size];
        let mut ans = 0;

        for &(r1, c1) in &ones1 {
            for &(r2, c2) in &ones2 {
                // 平移量：把 img1 的 (r1,c1) 对齐到 img2 的 (r2,c2)
                let dr = r2 - r1;
                let dc = c2 - c1;

                // 已经以该平移量计算过，跳过
                let idx = (dr + n as i32 - 1) as usize * size + (dc + n as i32 - 1) as usize;
                if seen[idx] {
                    continue;
                }
                seen[idx] = true;

                // 统计该平移下的重叠数
                let mut count = 0;
                for &(r, c) in &ones1 {
                    let nr = r + dr;
                    let nc = c + dc;
                    if nr >= 0 && nr < n as i32 && nc >= 0 && nc < n as i32
                        && has2[nr as usize * n + nc as usize]
                    {
                        count += 1;
                    }
                }
                ans = ans.max(count);
            }
        }

        ans
    }
}
```
