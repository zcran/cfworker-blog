---
title: "leetcode-二叉树60"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 找出克隆二叉树中的相同节点

给你两棵二叉树，原始树 original 和克隆树 cloned，以及一个位于原始树 original 中的目标节点 target。

其中，克隆树 cloned 是原始树 original 的一个 副本 。

请找出在树 cloned 中，与 target 相同 的节点，并返回对该节点的引用（在 C/C++ 等有指针的语言中返回 节点指针，其他语言返回节点本身）。



注意：你 不能 对两棵二叉树，以及 target 节点进行更改。只能 返回对克隆树 cloned 中已有的节点的引用。


C++
```
class Solution {
public:
    // 同步遍历 original 和 cloned，当 original 指针与 target 重合时，
    // 同一位置的 cloned 指针即为所求。
    TreeNode* getTargetCopy(TreeNode* original, TreeNode* cloned, TreeNode* target) {
        if (!original) return nullptr;
        if (original == target) return cloned;

        TreeNode* left = getTargetCopy(original->left, cloned->left, target);
        if (left) return left;

        return getTargetCopy(original->right, cloned->right, target);
    }
};
```
