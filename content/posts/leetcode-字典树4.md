---
title: "leetcode-字典树4"
date: 2026-06-02T10:23:06+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 单词搜索 II

给定一个 m x n 二维字符网格 board 和一个单词（字符串）列表 words， 返回所有二维网格上的单词 。

单词必须按照字母顺序，通过 相邻的单元格 内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母在一个单词中不允许被重复使用。

```
/// 前缀树（Trie）节点结构
/// 用于存储单词列表，支持高效前缀匹配
#[derive(Default)]
struct Trie {
    /// 标记当前节点是否为一个完整单词的结尾
    is_end: bool,

    /// 26 个子节点（a-z）
    /// Option 表示路径是否存在
    /// Box<Trie> 将节点存储在堆上，避免递归类型大小不确定
    childs: [Option<Box<Trie>>; 26],
}

impl Trie {
    /// 创建一个新的 Trie 节点
    fn new() -> Self {
        Default::default()
    }

    /// 向 Trie 中插入一个单词
    ///
    /// # 参数
    /// - `word`: 要插入的单词（只包含小写字母）
    fn insert(&mut self, word: String) {
        let mut cur = self;

        // 遍历单词的每个字节，转换为索引（'a' -> 0, 'b' -> 1, ...）
        for idx in word.bytes().map(|b| (b - b'a') as usize) {
            // get_or_insert 的逻辑：
            // - 如果 childs[idx] 存在，返回其可变引用
            // - 如果不存在，创建新节点并插入，然后返回其可变引用
            cur = cur.childs[idx]
                .get_or_insert(Box::new(Trie::new()))
                .as_mut();
        }

        // 标记最后一个节点为单词结尾
        cur.is_end = true;
    }
}

impl Solution {
    /// 在字母板中查找所有存在的单词
    ///
    /// # 参数
    /// - `board`: 二维字符网格
    /// - `words`: 要查找的单词列表
    ///
    /// # 返回
    /// 所有能在网格中找到的单词
    ///
    /// # 算法
    /// 1. 将所有单词构建成 Trie（前缀树）
    /// 2. 从网格的每个格子开始 DFS 搜索
    /// 3. 利用 Trie 进行剪枝，避免无效搜索
    pub fn find_words(board: Vec<Vec<char>>, words: Vec<String>) -> Vec<String> {
        let mut root = Trie::new();

        // 步骤 1: 将所有单词插入 Trie
        // 这样可以在搜索时快速判断当前路径是否是某个单词的前缀
        for word in words.into_iter() {
            root.insert(word);
        }

        // 步骤 2: 准备 DFS 搜索
        let (m, n) = (board.len(), board[0].len());
        let mut s = String::new();          // 当前路径组成的字符串
        let mut result = vec![];             // 存储找到的单词
        let mut visited = vec![vec![false; n]; m];  // 标记格子是否已访问

        // 从每个格子出发进行 DFS
        for i in 0..m {
            for j in 0..n {
                Self::dfs(
                    &board, i, j,
                    &mut root,      // 当前 Trie 节点（可变引用）
                    &mut s,
                    &mut result,
                    &mut visited
                );
            }
        }

        result
    }

    /// 深度优先搜索（DFS）回溯算法
    ///
    /// # 参数
    /// - `board`: 字母板
    /// - `x, y`: 当前格子坐标
    /// - `cur`: 当前 Trie 节点（表示已匹配的前缀）
    /// - `s`: 当前路径组成的字符串
    /// - `result`: 存储结果的容器
    /// - `visited`: 访问标记矩阵
    fn dfs(
        board: &Vec<Vec<char>>,
        x: usize, y: usize,
        mut cur: &mut Trie,   // mut 表示可以修改 cur 指向的节点
        s: &mut String,
        result: &mut Vec<String>,
        visited: &mut Vec<Vec<bool>>
    ) {
        // 计算当前字符在 Trie 子节点中的索引
        let idx = (board[x][y] as u8 - b'a') as usize;

        // 剪枝优化：如果当前字符对应的路径不存在，直接返回
        if cur.childs[idx].is_none() {
            return;
        }

        // 移动到下一个 Trie 节点
        cur = cur.childs[idx].as_mut().unwrap().as_mut();

        // 标记当前格子已访问
        visited[x][y] = true;
        // 将当前字符加入路径字符串
        s.push(board[x][y]);

        // 如果当前节点是单词结尾，说明找到了一个完整单词
        if cur.is_end {
            result.push(s.clone());   // 将单词加入结果集
            cur.is_end = false;       // 去重：避免重复添加同一个单词
        }

        // 向四个方向继续 DFS（上、下、左、右）
        // 条件：不超出边界，且未访问过

        // 向上
        if x > 0 && !visited[x - 1][y] {
            Self::dfs(board, x - 1, y, cur, s, result, visited);
        }
        // 向下
        if x + 1 < board.len() && !visited[x + 1][y] {
            Self::dfs(board, x + 1, y, cur, s, result, visited);
        }
        // 向左
        if y > 0 && !visited[x][y - 1] {
            Self::dfs(board, x, y - 1, cur, s, result, visited);
        }
        // 向右
        if y + 1 < board[0].len() && !visited[x][y + 1] {
            Self::dfs(board, x, y + 1, cur, s, result, visited);
        }

        // 回溯：恢复状态，以便其他路径使用
        visited[x][y] = false;   // 取消标记
        s.pop();                  // 移除当前字符
    }
}
```
