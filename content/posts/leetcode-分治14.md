---
title: "leetcode-分治14"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 建立四叉树


给你一个 n * n 矩阵 grid ，矩阵由若干 0 和 1 组成。请你用四叉树表示该矩阵 grid 。

你需要返回能表示矩阵 grid 的 四叉树 的根结点。

四叉树数据结构中，每个内部节点只有四个子节点。此外，每个节点都有两个属性：

· val：储存叶子结点所代表的区域的值。1 对应 True，0 对应 False。注意，当 isLeaf 为 False 时，你可以把 True 或者 False 赋值给节点，两种值都会被判题机制 接受 。

· isLeaf: 当这个节点是一个叶子结点时为 True，如果它有 4 个子节点则为 False 。
    class Node {
        public boolean val;
        public boolean isLeaf;
        public Node topLeft;
        public Node topRight;
        public Node bottomLeft;
        public Node bottomRight;
    }

我们可以按以下步骤为二维区域构建四叉树：

1.如果当前网格的值相同（即，全为 0 或者全为 1），将 isLeaf 设为 True ，将 val 设为网格相应的值，并将四个子节点都设为 Null 然后停止。
2.如果当前网格的值不同，将 isLeaf 设为 False， 将 val 设为任意值，然后如下图所示，将当前网格划分为四个子网格。
3.使用适当的子网格递归每个子节点。


四叉树格式：

输出为使用层序遍历后四叉树的序列化形式，其中 null 表示路径终止符，其下面不存在节点。

它与二叉树的序列化非常相似。唯一的区别是节点以列表形式表示 [isLeaf, val] 。

如果 isLeaf 或者 val 的值为 True ，则表示它在列表 [isLeaf, val] 中的值为 1 ；如果 isLeaf 或者 val 的值为 False ，则表示值为 0 。

C++
```
class Solution {
public:
    Node* construct(vector<vector<int>>& grid) {
        int n = grid.size();

        // 构建前缀和数组 pre，pre[i][j] 表示从 grid[0][0] 到 grid[i-1][j-1] 的矩形和
        // 多开一行一列（索引从 1 开始），避免边界判断
        vector<vector<int>> pre(n + 1, vector<int>(n + 1, 0));

        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= n; ++j) {
                // 二维前缀和公式：
                // 当前块的和 = 上方块和 + 左方块和 - 左上角块和 + 当前格子的值
                pre[i][j] = pre[i - 1][j] + pre[i][j - 1] - pre[i - 1][j - 1] + grid[i - 1][j - 1];
            }
        }

        // 获取子矩阵的和（左闭右开区间：[r0, r1) × [c0, c1)）
        auto getSum = [&](int r0, int c0, int r1, int c1) -> int {
            return pre[r1][c1] - pre[r1][c0] - pre[r0][c1] + pre[r0][c0];
        };

        // 深度优先递归构建四叉树
        // 参数含义：当前处理的子矩阵范围为行 [r0, r1)，列 [c0, c1)
        function<Node*(int, int, int, int)> dfs = [&](int r0, int c0, int r1, int c1) -> Node* {
            int total = getSum(r0, c0, r1, c1);          // 子矩阵中 1 的个数
            int area = (r1 - r0) * (c1 - c0);            // 子矩阵总面积

            // 情况1：全为 0 → 叶子节点，值为 false
            if (total == 0) {
                return new Node(false, true);
            }

            // 情况2：全为 1 → 叶子节点，值为 true
            if (total == area) {
                return new Node(true, true);
            }

            // 情况3：有 0 也有 1 → 内部节点，需要继续划分
            int midRow = (r0 + r1) / 2;   // 行的中点（向下取整）
            int midCol = (c0 + c1) / 2;   // 列的中点（向下取整）

            return new Node(
                true,                     // 内部节点的 val 可以任意，此处设为 true
                false,                    // isLeaf = false，表示有子节点
                dfs(r0, c0, midRow, midCol),                      // 左上象限
                dfs(r0, midCol, midRow, c1),                      // 右上象限
                dfs(midRow, c0, r1, midCol),                      // 左下象限
                dfs(midRow, midCol, r1, c1)                       // 右下象限
            );
        };

        // 从整个矩阵开始构建
        return dfs(0, 0, n, n);
    }
};
```




rust
```
// 四叉树节点定义
// #[derive(Debug, PartialEq, Eq)]
// pub struct Node {
//     pub val: bool,
//     pub is_leaf: bool,
//     pub top_left: Option<Box<Node>>,
//     pub top_right: Option<Box<Node>>,
//     pub bottom_left: Option<Box<Node>>,
//     pub bottom_right: Option<Box<Node>>,
// }

impl Solution {
    /// 使用四叉树表示矩阵
    ///
    /// 算法：递归分治 + 前缀和优化
    /// 1. 构建前缀和数组，O(1) 时间获取子矩阵和
    /// 2. 递归划分矩阵为四个子区域
    /// 3. 如果子区域全0或全1，创建叶子节点；否则继续划分
    pub fn construct(grid: Vec<Vec<i32>>) -> Option<Box<Node>> {
        let n = grid.len();
        if n == 0 {
            return None;
        }

        // 构建前缀和数组，便于快速计算子矩阵和
        // prefix[i+1][j+1] 表示 grid[0..i][0..j] 的和
        let mut prefix = vec![vec![0; n + 1]; n + 1];
        for i in 0..n {
            let mut row_sum = 0;
            for j in 0..n {
                row_sum += grid[i][j];
                // 前缀和公式：当前块 = 上边 + 左边 - 左上角 + 当前值
                prefix[i + 1][j + 1] = prefix[i][j + 1] + row_sum;
            }
        }

        // 闭包：获取子矩阵的和（区间为 [r0, r1) x [c0, c1)）
        let get_sum = |r0: usize, c0: usize, r1: usize, c1: usize| -> i32 {
            prefix[r1][c1] - prefix[r1][c0] - prefix[r0][c1] + prefix[r0][c0]
        };

        // 递归构建四叉树
        fn build(
            prefix: &[Vec<i32>],
            r0: usize, c0: usize,  // 左上角坐标（包含）
            r1: usize, c1: usize,  // 右下角坐标（不包含）
        ) -> Option<Box<Node>> {
            let total = prefix[r1][c1] - prefix[r1][c0] - prefix[r0][c1] + prefix[r0][c0];
            let area = ((r1 - r0) * (c1 - c0)) as i32;

            if total == 0 {
                // 全0区域：叶子节点，值为 false
                return Some(Box::new(Node {
                    val: false,
                    is_leaf: true,
                    top_left: None,
                    top_right: None,
                    bottom_left: None,
                    bottom_right: None,
                }));
            }

            if total == area {
                // 全1区域：叶子节点，值为 true
                return Some(Box::new(Node {
                    val: true,
                    is_leaf: true,
                    top_left: None,
                    top_right: None,
                    bottom_left: None,
                    bottom_right: None,
                }));
            }

            // 混合区域：非叶子节点，继续划分
            let mid_row = (r0 + r1) / 2;
            let mid_col = (c0 + c1) / 2;

            Some(Box::new(Node {
                val: true,  // 非叶子节点的 val 可以任意设置
                is_leaf: false,
                top_left: build(prefix, r0, c0, mid_row, mid_col),
                top_right: build(prefix, r0, mid_col, mid_row, c1),
                bottom_left: build(prefix, mid_row, c0, r1, mid_col),
                bottom_right: build(prefix, mid_row, mid_col, r1, c1),
            }))
        }

        build(&prefix, 0, 0, n, n)
    }
}
```
