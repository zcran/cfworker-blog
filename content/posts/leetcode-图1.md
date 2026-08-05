---
title: "leetcode-图1"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 克隆图

给你无向 连通 图中一个节点的引用，请你返回该图的 深拷贝（克隆）。

图中的每个节点都包含它的值 val（int） 和其邻居的列表（list[Node]）。

class Node {
    public int val;
    public List<Node> neighbors;
}


测试用例格式：

简单起见，每个节点的值都和它的索引相同。例如，第一个节点值为 1（val = 1），第二个节点值为 2（val = 2），以此类推。该图在测试用例中使用邻接列表表示。

邻接列表 是用于表示有限图的无序列表的集合。每个列表都描述了图中节点的邻居集。

给定节点将始终是图中的第一个节点（值为 1）。你必须将 给定节点的拷贝 作为对克隆图的引用返回。

C++
```
class Solution {
public:
    // 哈希表：记录原节点指针 -> 克隆节点指针的映射
    // 用途：① 防止重复克隆（处理环状图） ② 快速返回已克隆的节点
    unordered_map<Node*, Node*> clonedMap;

    /**
     * 克隆整个图（深度优先遍历方式）
     * @param node 原图的任意一个节点（入口）
     * @return 返回该节点对应的克隆节点
     */
    Node* cloneGraph(Node* node) {
        // 情况1：空图，直接返回空指针
        if (node == nullptr) {
            return nullptr;
        }

        // 情况2：当前节点已被克隆过，直接返回其克隆节点
        // 此处理解环状引用，例如 A->B 且 B->A，第二次遇到A时直接返回
        auto it = clonedMap.find(node);
        if (it != clonedMap.end()) {
            return it->second;
        }

        // 情况3：首次遇到该节点，执行克隆流程

        // 步骤1：创建克隆节点（仅复制节点值，邻居列表暂为空）
        Node* clonedNode = new Node(node->val);

        // 步骤2：将原节点与克隆节点的映射关系存入哈希表
        // 必须放在递归前，这样环状引用时能正确返回当前克隆节点
        clonedMap[node] = clonedNode;

        // 步骤3：递归克隆所有邻居节点
        // 遍历原节点的每个邻居，深度克隆后添加到克隆节点的邻居列表中
        for (Node* neighbor : node->neighbors) {
            Node* clonedNeighbor = cloneGraph(neighbor);
            clonedNode->neighbors.push_back(clonedNeighbor);
        }

        // 步骤4：返回完整的克隆节点
        return clonedNode;
    }
};
```
