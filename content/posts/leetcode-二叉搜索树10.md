---
title: "leetcode-二叉搜索树10"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---



## 序列化和反序列化二叉搜索树


序列化是将数据结构或对象转换为一系列位的过程，以便它可以存储在文件或内存缓冲区中，或通过网络连接链路传输，以便稍后在同一个或另一个计算机环境中重建。

设计一个算法来序列化和反序列化 二叉搜索树 。 对序列化/反序列化算法的工作方式没有限制。 您只需确保二叉搜索树可以序列化为字符串，并且可以将该字符串反序列化为最初的二叉搜索树。

编码的字符串应尽可能紧凑。


```
use std::rc::Rc;
use std::cell::RefCell;

struct Codec;

impl Codec {
    /// 创建编解码器实例
    fn new() -> Self {
        Codec
    }

    /// 序列化：前序遍历 BST，值用逗号分隔（利用 BST 性质，无需记录空节点）
    fn serialize(&self, root: Option<Rc<RefCell<TreeNode>>>) -> String {
        let mut out = String::new();
        Self::serialize_into(&root, &mut out);
        out
    }

    // 递归辅助：前序遍历并将值写入字符串（避免多次分配）
    fn serialize_into(node: &Option<Rc<RefCell<TreeNode>>>, out: &mut String) {
        if let Some(n) = node {
            let n = n.borrow();
            if !out.is_empty() {
                out.push(',');
            }
            out.push_str(&n.val.to_string());
            Self::serialize_into(&n.left, out);
            Self::serialize_into(&n.right, out);
        }
    }

    /// 反序列化：根据前序遍历结果和 BST 范围重建树
    fn deserialize(&self, data: String) -> Option<Rc<RefCell<TreeNode>>> {
        if data.is_empty() {
            return None;
        }
        let vals: Vec<i32> = data.split(',').map(|s| s.parse().unwrap()).collect();
        Self::build_from_slice(&vals, i32::MIN, i32::MAX).map(|(node, _)| node)
    }

    // 纯函数递归：给定值切片和允许的范围，返回 (子树根节点, 剩余未使用的切片)
    fn build_from_slice(vals: &[i32], min: i32, max: i32) -> Option<(Rc<RefCell<TreeNode>>, &[i32])> {
        let (first, rest) = vals.split_first()?;
        if *first < min || *first > max {
            return None;
        }
        // 构建左子树：使用更紧的上界 first
        let (left, rest) = Self::build_from_slice(rest, min, *first)
            .map(|(l, r)| (Some(l), r))
            .unwrap_or((None, rest));
        // 构建右子树：使用更紧的下界 first
        let (right, rest) = Self::build_from_slice(rest, *first, max)
            .map(|(r, rrest)| (Some(r), rrest))
            .unwrap_or((None, rest));
        let node = Rc::new(RefCell::new(TreeNode {
            val: *first,
            left,
            right,
        }));
        Some((node, rest))
    }
}
```
