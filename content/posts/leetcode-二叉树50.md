---
title: "leetcode-二叉树50"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树寻路

在一棵无限的二叉树上，每个节点都有两个子节点，树中的节点 逐行 依次按 “之” 字形进行标记。

如下图所示，在奇数行（即，第一行、第三行、第五行……）中，按从左到右的顺序进行标记；

而偶数行（即，第二行、第四行、第六行……）中，按从右到左的顺序进行标记。
![](../pic/tree.png)

给你树上某一个节点的标号 label，请你返回从根节点到该标号为 label 节点的路径，该路径是由途经的节点标号所组成的。


```
impl Solution {
    /// 在之字形标记的完美二叉树中，返回从根到 label 的路径。
    ///
    /// 完美二叉树第 row 行（从1开始）的范围是 [2^(row-1), 2^row - 1]。
    /// 奇数行从左到右，偶数行从右到左。利用对称性：
    /// 偶数行中 label 的镜像位置 = 行首 + 行尾 - label。
    ///
    /// 策略：先将 label 映射到正常顺序下的位置，然后不断除以2找父节点，
    /// 再根据每行的奇偶性映射回之字形显示值。
    pub fn path_in_zig_zag_tree(label: i32) -> Vec<i32> {
        // 行号 = bit_length，因为第 row 行最小值是 2^(row-1)
        let mut row = 32 - label.leading_zeros() as i32;
        let mut pos = label;

        // 若起始行为偶数，先把 label 镜像到正常顺序
        if row % 2 == 0 {
            pos = Self::mirror(pos, row);
        }

        let mut path = Vec::with_capacity(row as usize);

        // 从叶子向上遍历到根，记录路径
        while row > 0 {
            if row % 2 == 0 {
                path.push(Self::mirror(pos, row));
            } else {
                path.push(pos);
            }
            row -= 1;
            pos >>= 1; // 父节点在正常顺序下的位置
        }

        path.reverse();
        path
    }

    /// 计算 label 在第 row 行之字形标记中的镜像位置。
    fn mirror(label: i32, row: i32) -> i32 {
        let start = 1 << (row - 1);
        let end = (1 << row) - 1;
        start + end - label
    }
}
```
