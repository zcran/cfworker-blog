---
title: "leetcode-滚动哈希24"
date: 2026-04-17T20:00:32+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


##  统计水平子串和垂直子串重叠格子的数目

给你一个由字符组成的 m x n 矩阵 grid 和一个字符串 pattern。

水平子串 是从左到右的一段连续字符序列。如果子串到达了某行的末尾，它将换行并从下一行的第一个字符继续。不会 从最后一行回到第一行。

垂直子串 是从上到下的一段连续字符序列。如果子串到达了某列的底部，它将换列并从下一列的第一个字符继续。不会 从最后一列回到第一列。

请统计矩阵中满足以下条件的单元格数量：

该单元格必须属于 至少 一个等于 pattern 的水平子串，且属于 至少 一个等于 pattern 的垂直子串。
返回满足条件的单元格数量。

```
impl Solution {
    /// 统计网格中同时位于水平方向匹配区间和垂直方向匹配区间内的单元格数量
    ///
    /// # 参数
    /// - `g`: 字符网格，`Vec<Vec<char>>` 形式
    /// - `pat`: 待匹配的模式字符串
    ///
    /// # 返回
    /// 满足条件的单元格个数
    pub fn count_cells(g: Vec<Vec<char>>, pat: String) -> i32 {
        let n = g.len();                 // 行数
        let m = g[0].len();              // 列数
        let pat_chars: Vec<char> = pat.chars().collect();

        // 1. 计算模式串的 KMP next 数组
        let next = Self::compute_kmp_next(&pat_chars);

        // 2. 将网格按行优先展平为一维序列，查找所有匹配起始位置，并合并重叠区间
        let horizontal_intervals = {
            let flattened = Self::flatten_row_major(&g);
            let matches = Self::find_all_matches(&flattened, &pat_chars, &next);
            Self::merge_overlapping_intervals(matches, pat_chars.len())
        };

        // 3. 将网格按列优先展平为一维序列，查找匹配并合并区间
        let vertical_intervals = {
            let flattened = Self::flatten_col_major(&g);
            let matches = Self::find_all_matches(&flattened, &pat_chars, &next);
            Self::merge_overlapping_intervals(matches, pat_chars.len())
        };

        // 4. 遍历每个单元格，判断其对应的水平索引和垂直索引是否同时落在对应区间内
        (0..n * m)
            .filter(|&idx| {
                let row = idx / m;
                let col = idx % m;
                let horizontal_idx = row * m + col;        // 水平展平时的索引
                let vertical_idx = col * n + row;          // 垂直展平时的索引

                Self::is_in_intervals(horizontal_idx, &horizontal_intervals)
                    && Self::is_in_intervals(vertical_idx, &vertical_intervals)
            })
            .count() as i32
    }

    /// 计算 KMP 算法的 next 数组（前缀函数）
    /// `next[i]` 表示 `pat[..=i]` 的最长公共前后缀长度
    fn compute_kmp_next(pat: &[char]) -> Vec<usize> {
        let mut next = vec![0; pat.len()];
        let mut j = 0;
        for i in 1..pat.len() {
            while j > 0 && pat[i] != pat[j] {
                j = next[j - 1];
            }
            if pat[i] == pat[j] {
                j += 1;
            }
            next[i] = j;
        }
        next
    }

    /// 在文本序列 `text` 中查找模式串 `pat` 的所有出现位置（起始索引）
    /// 使用预计算的 `next` 数组进行 KMP 搜索
    fn find_all_matches(text: &[char], pat: &[char], next: &[usize]) -> Vec<usize> {
        let mut matches = Vec::new();
        let mut j = 0;
        for (i, &ch) in text.iter().enumerate() {
            while j > 0 && ch != pat[j] {
                j = next[j - 1];
            }
            if ch == pat[j] {
                j += 1;
                if j == pat.len() {
                    matches.push(i + 1 - pat.len());
                    j = next[j - 1];
                }
            }
        }
        matches
    }

    /// 将网格按行优先（逐行拼接）展平为一维字符向量
    fn flatten_row_major(g: &[Vec<char>]) -> Vec<char> {
        g.iter().flatten().copied().collect()
    }

    /// 将网格按列优先（逐列拼接）展平为一维字符向量
    fn flatten_col_major(g: &[Vec<char>]) -> Vec<char> {
        let n = g.len();
        let m = g[0].len();
        (0..m)
            .flat_map(|col| (0..n).map(move |row| g[row][col]))
            .collect()
    }

    /// 合并重叠或连续的匹配区间
    /// 输入为匹配起始索引列表 `starts` 和模式长度 `pat_len`，
    /// 返回合并后的区间列表 `(start, end)`，其中 `end` 为闭区间结束下标。
    fn merge_overlapping_intervals(mut starts: Vec<usize>, pat_len: usize) -> Vec<(usize, usize)> {
        if starts.is_empty() {
            return Vec::new();
        }
        // 匹配位置已按顺序出现，但保险起见排序一次
        starts.sort_unstable();

        let mut merged = Vec::new();
        let mut current_start = starts[0];
        let mut current_end = current_start + pat_len - 1;

        for &start in starts.iter().skip(1) {
            let end = start + pat_len - 1;
            if start <= current_end + 1 { // +1 表示相邻区间也合并（原逻辑中为 start <= current_end）
                // 区间重叠或相邻，扩展当前区间
                current_end = current_end.max(end);
            } else {
                // 无重叠，保存当前区间并开始新区间
                merged.push((current_start, current_end));
                current_start = start;
                current_end = end;
            }
        }
        merged.push((current_start, current_end));
        merged
    }

    /// 判断给定的索引 `pos` 是否落在任意一个区间 `intervals` 内
    /// 使用二分查找提高效率（intervals 已按起点有序）
    fn is_in_intervals(pos: usize, intervals: &[(usize, usize)]) -> bool {
        if intervals.is_empty() {
            return false;
        }
        // 二分查找最后一个起点 <= pos 的区间
        let mut l = 0;
        let mut r = intervals.len() - 1;
        while l < r {
            let mid = (l + r + 1) >> 1;
            if intervals[mid].0 <= pos {
                l = mid;
            } else {
                r = mid - 1;
            }
        }
        let (start, end) = intervals[l];
        pos >= start && pos <= end
    }
}
```
