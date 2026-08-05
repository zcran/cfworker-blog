---
title: "leetcode-链表9"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 填充每个节点的下一个右侧节点指针 II

给定一个二叉树：

struct Node {
  int val;
  Node *left;
  Node \*right;
  Node \*next;
}
填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL 。

初始状态下，所有 next 指针都被设置为 NULL 。


C++
```
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    /**
     * 填充每个节点的下一个右侧节点指针（完美二叉树）
     *
     * 算法思路：层序遍历（广度优先搜索）
     * 1. 使用队列按层遍历二叉树
     * 2. 对每一层的节点，从左到右依次连接 next 指针
     * 3. 当前层处理完成后，将下一层所有节点加入队列
     *
     * 时间复杂度：O(n)，每个节点访问一次
     * 空间复杂度：O(n)，队列在最坏情况下存储最后一层节点（约 n/2）
     */
    Node* connect(Node* root) {
        // 处理空树的情况
        if (root == nullptr) {
            return nullptr;
        }

        // 创建队列用于层序遍历
        queue<Node*> q;
        q.push(root);

        // 逐层处理
        while (!q.empty()) {
            // 记录当前层的节点数量
            int levelSize = q.size();

            // 处理当前层的所有节点
            for (int i = 0; i < levelSize; i++) {
                // 取出当前节点
                Node* currentNode = q.front();
                q.pop();

                // 如果不是当前层的最后一个节点
                // 将其 next 指向队列中的下一个节点（同一层的下一个节点）
                if (i < levelSize - 1) {
                    currentNode->next = q.front();
                }
                // 如果是最后一个节点，next 默认为 nullptr，无需额外操作

                // 将当前节点的左右子节点加入队列（下一层）
                if (currentNode->left != nullptr) {
                    q.push(currentNode->left);
                }
                if (currentNode->right != nullptr) {
                    q.push(currentNode->right);
                }
            }
        }

        // 返回根节点
        return root;
    }
};
```
