---
title: "leetcode-广度优先搜索15"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 图像渲染

有一幅以 m x n 的二维整数数组表示的图画 image ，其中 image[i][j] 表示该图画的像素值大小。你也被给予三个整数 sr ,  sc 和 color 。你应该从像素 image[sr][sc] 开始对图像进行上色 填充 。

为了完成 上色工作：

1. 从初始像素开始，将其颜色改为 color。
2. 对初始坐标的 上下左右四个方向上 相邻且与初始像素的原始颜色同色的像素点执行相同操作。
3. 通过检查与初始像素的原始颜色相同的相邻像素并修改其颜色来继续 重复 此过程。
4. 当 没有 其它原始颜色的相邻像素时 停止 操作。

最后返回经过上色渲染 修改 后的图像 。


```
impl Solution {
    pub fn flood_fill(mut image: Vec<Vec<i32>>, sr: i32, sc: i32, new_color: i32) -> Vec<Vec<i32>> {
        let (sr, sc) = (sr as usize, sc as usize);
        let old_color = image[sr][sc];

        // 颜色相同则无需处理，同时避免重复入栈造成的死循环
        if old_color == new_color {
            return image;
        }

        let m = image.len();
        let n = image[0].len();

        // 显式栈做 DFS，原地染色，避免额外 visited 数组
        let mut stack = vec![(sr, sc)];
        image[sr][sc] = new_color;

        while let Some((r, c)) = stack.pop() {
            for (nr, nc) in [
                (r.wrapping_sub(1), c),
                (r + 1, c),
                (r, c.wrapping_sub(1)),
                (r, c + 1),
            ] {
                if nr < m && nc < n && image[nr][nc] == old_color {
                    image[nr][nc] = new_color;
                    stack.push((nr, nc));
                }
            }
        }

        image
    }
}
```
