---
title: "leetcode-字典树19"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 搜索推荐系统

给你一个产品数组 products 和一个字符串 searchWord ，products  数组中每个产品都是一个字符串。

请你设计一个推荐系统，在依次输入单词 searchWord 的每一个字母后，推荐 products 数组中前缀与 searchWord 相同的最多三个产品。如果前缀相同的可推荐产品超过三个，请按字典序返回最小的三个。

请你以二维列表的形式，返回在输入 searchWord 每个字母后相应的推荐产品的列表。

```
// 定义前缀树（Trie）结构
// 生命周期 'a 表示存储的字符串引用的生命周期
struct Trie<'a> {
    // 26 个子节点（对应 a-z 字母）
    charset: [Option<Box<Trie<'a>>>; 26],

    // 存储当前节点对应的前 3 个推荐产品（按字典序排列）
    // 优化：每个节点只保存最多 3 个产品，避免遍历整个子树
    products: [Option<&'a str>; 3],

    // 标记当前节点是否为一个完整单词的结尾
    is_word: bool,
}

impl<'a> Trie<'a> {
    // 插入产品到前缀树
    fn insert(&mut self, new_product: &'a str) {
        let mut node = self;  // 从根节点开始

        // 遍历产品名称的每个字符
        for ch in new_product.bytes() {
            let idx = (ch - b'a') as usize;  // 计算字符索引

            // 如果子节点不存在，创建新节点
            if node.charset[idx].is_none() {
                node.charset[idx] = Some(Box::new(Trie {
                    charset: [const { None }; 26],  // 初始化子节点数组为空
                    products: [None; 3],             // 产品列表为空
                    is_word: false,
                }));
            }

            // 移动到子节点
            node = node.charset[idx].as_mut().unwrap();

            // 维护当前节点的前 3 个产品（按字典序排序）
            for i in 0..3 {
                if node.products[i].is_some() {
                    // 已有产品，需要比较并可能插入
                    use std::cmp::Ordering;
                    match new_product.cmp(node.products[i].unwrap()) {
                        Ordering::Less => {
                            // 新产品字典序更小，需要将后面的产品后移
                            for j in (i + 1..3).rev() {
                                node.products[j] = node.products[j - 1];
                            }
                            node.products[i] = Some(new_product);
                            break;  // 插入完成，退出循环
                        }
                        _ => {}  // 继续检查下一个位置
                    }
                } else {
                    // 找到空位，直接插入
                    node.products[i] = Some(new_product);
                    break;
                }
            }
        }
        node.is_word = true;  // 标记单词结束
    }

    // 搜索推荐产品
    fn search(&self, word: &str) -> Vec<Vec<String>> {
        let mut res = vec![];       // 存储每个前缀对应的推荐列表
        let mut node = self;        // 当前节点
        let mut is_invalid = false; // 标记是否已经遇到不存在的路径

        // 遍历搜索词的每个字符（前缀）
        for ch in word.bytes() {
            let idx = (ch - b'a') as usize;

            // 情况1：当前字符的路径不存在
            if node.charset[idx].is_none() {
                is_invalid = true;   // 标记无效，后续所有前缀都无推荐
                res.push(vec![]);    // 当前前缀无推荐
                continue;
            }
            // 情况2：已经无效，后续继续添加空列表
            else if is_invalid {
                res.push(vec![]);
                continue;
            }

            // 情况3：路径存在，获取推荐产品
            node = node.charset[idx].as_ref().unwrap();
            let mut inner_res = vec![];

            // 收集当前节点的前 3 个推荐产品
            for i in 0..3 {
                if node.products[i].is_none() {
                    break;  // 没有更多产品了
                }
                if let Some(p) = node.products[i] {
                    inner_res.push(p.into());  // &str -> String
                }
            }
            res.push(inner_res);
        }
        res
    }
}

impl Solution {
    pub fn suggested_products(products: Vec<String>, search_word: String) -> Vec<Vec<String>> {
        // 创建前缀树根节点
        let mut trie = Trie {
            charset: [const { None }; 26],
            products: [None; 3],
            is_word: false,
        };

        // 插入所有产品
        for p in &products {
            trie.insert(p);
        }

        // 搜索并返回推荐结果
        trie.search(&search_word)
    }
}
```
