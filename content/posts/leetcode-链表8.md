---
title: "leetcode-链表8"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 填充每个节点的下一个右侧节点指针

给定一个 完美二叉树 ，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

struct Node {
  int val;
  Node *left;
  Node \*right;
  Node \*next;
}

填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。

初始状态下，所有 next 指针都被设置为 NULL。


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
     * 思路：层序遍历，将每层节点从左到右连接起来
     *
     * 时间复杂度：O(n)，每个节点访问一次
     * 空间复杂度：O(n)，队列最多存储一层节点
     */
    Node* connect(Node* root) {
        // 空树直接返回
        if (root == nullptr) {
            return nullptr;
        }

        // 使用队列进行层序遍历
        queue<Node*> q;
        q.push(root);

        while (!q.empty()) {
            int levelSize = q.size();  // 当前层的节点数量

            // 处理当前层的所有节点
            for (int i = 0; i < levelSize; ++i) {
                Node* curr = q.front();
                q.pop();

                // 如果不是当前层的最后一个节点，将 next 指向队列中的下一个节点
                if (i < levelSize - 1) {
                    curr->next = q.front();
                }
                // 当前层最后一个节点的 next 已默认为 NULL，无需额外操作

                // 将下一层节点加入队列
                if (curr->left != nullptr) {
                    q.push(curr->left);
                }
                if (curr->right != nullptr) {
                    q.push(curr->right);
                }
            }
        }

        return root;
    }
};
```
