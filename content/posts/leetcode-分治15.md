---
title: "leetcode-分治15"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 四叉树交集

二进制矩阵中的所有元素不是 0 就是 1 。

给你两个四叉树，quadTree1 和 quadTree2。其中 quadTree1 表示一个 n * n 二进制矩阵，而 quadTree2 表示另一个 n * n 二进制矩阵。

请你返回一个表示 n * n 二进制矩阵的四叉树，它是 quadTree1 和 quadTree2 所表示的两个二进制矩阵进行 按位逻辑或运算 的结果。

注意，当 isLeaf 为 False 时，你可以把 True 或者 False 赋值给节点，两种值都会被判题机制 接受 。

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



C++
```
class Solution {
public:
    /**
     * 对两个四叉树进行逻辑或（OR）运算
     * 规则：
     * - 如果任一节点为叶子且值为 true，结果直接为 true
     * - 否则递归合并四个象限，若四个子节点都为叶子且值相同，则合并为一个叶子节点
     */
    Node* intersect(Node* quadTree1, Node* quadTree2) {
        // 情况1：quadTree1 是叶子节点
        if (quadTree1->isLeaf) {
            // 如果 quadTree1 的值为 true，则结果直接为 true（叶子节点）
            if (quadTree1->val) {
                return new Node(true, true);
            }
            // 如果 quadTree1 的值为 false，则结果取决于 quadTree2
            // 直接复制 quadTree2 的整个子树（浅拷贝）
            return new Node(quadTree2->val, quadTree2->isLeaf,
                            quadTree2->topLeft, quadTree2->topRight,
                            quadTree2->bottomLeft, quadTree2->bottomRight);
        }

        // 情况2：quadTree2 是叶子节点（对称处理，交换参数后复用情况1的逻辑）
        if (quadTree2->isLeaf) {
            return intersect(quadTree2, quadTree1);
        }

        // 情况3：两个节点都不是叶子 → 递归合并四个对应的象限
        Node* topLeft     = intersect(quadTree1->topLeft,     quadTree2->topLeft);
        Node* topRight    = intersect(quadTree1->topRight,    quadTree2->topRight);
        Node* bottomLeft  = intersect(quadTree1->bottomLeft,  quadTree2->bottomLeft);
        Node* bottomRight = intersect(quadTree1->bottomRight, quadTree2->bottomRight);

        // 优化：如果四个子节点都是叶子且值全部相等，则可以合并为一个叶子节点
        bool allLeaves = topLeft->isLeaf && topRight->isLeaf && bottomLeft->isLeaf && bottomRight->isLeaf;
        bool allSameVal = allLeaves && (topLeft->val == topRight->val) &&
                          (topLeft->val == bottomLeft->val) && (topLeft->val == bottomRight->val);

        if (allSameVal) {
            // 四个子节点完全相同 → 合并为一个叶子节点
            return new Node(topLeft->val, true);
        }

        // 否则：创建一个内部节点，包含四个子节点
        return new Node(false, false, topLeft, topRight, bottomLeft, bottomRight);
    }
};
```
