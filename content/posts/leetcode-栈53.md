---
title: "leetcode-栈53"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 子序列最大优雅度

给你一个长度为 n 的二维整数数组 items 和一个整数 k 。

items[i] = [profiti, categoryi]，其中 profiti 和 categoryi 分别表示第 i 个项目的利润和类别。

现定义 items 的 子序列 的 优雅度 可以用 total_profit + distinct_categories2 计算，其中 total_profit 是子序列中所有项目的利润总和，distinct_categories 是所选子序列所含的所有类别中不同类别的数量。

你的任务是从 items 所有长度为 k 的子序列中，找出 最大优雅度 。

用整数形式表示并返回 items 中所有长度恰好为 k 的子序列的最大优雅度。

注意：数组的子序列是经由原数组删除一些元素（可能不删除）而产生的新数组，且删除不改变其余元素相对顺序。


```
impl Solution {
    /// 返回长度为 k 的子序列的最大优雅度
    ///
    /// # 算法思路
    /// 1. 按利润降序排序，优先选择利润最高的 k 个项目
    /// 2. 记录已选类别的集合，将重复类别的项目利润存入替换池
    /// 3. 遍历剩余项目，若遇到新类别且替换池非空，则用当前项目替换掉重复类别中**利润最小**的项目
    ///    （因为排序降序，替换池中最后加入的利润最小，使用 Vec 的 pop 即可取出）
    /// 4. 每次更新总利润和类别数，计算优雅度并取最大值
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n log n)，主要为排序开销
    /// - 空间复杂度：O(n)，哈希表和替换池
    pub fn find_maximum_elegance(mut items: Vec<Vec<i32>>, k: i32) -> i64 {
        let k = k as usize;

        // 按利润降序排序
        items.sort_unstable_by(|a, b| b[0].cmp(&a[0]));

        let mut used_categories = std::collections::HashSet::with_capacity(k);
        let mut replace_pool = Vec::new(); // 存储重复项目的利润（降序，末尾为最小利润）
        let mut total_profit = 0i64;

        // 第一步：选择前 k 个利润最高的项目
        for i in 0..k {
            let profit = items[i][0] as i64;
            let category = items[i][1];

            total_profit += profit;

            if used_categories.contains(&category) {
                // 类别重复，记录该项目的利润，后续可被替换
                replace_pool.push(profit);
            } else {
                used_categories.insert(category);
            }
        }

        let mut max_elegance = total_profit + (used_categories.len() as i64).pow(2);

        // 第二步：尝试用剩余项目替换重复类别中利润最小的项目，以增加类别数
        for i in k..items.len() {
            let profit = items[i][0] as i64;
            let category = items[i][1];

            // 只有遇到新类别且还有可替换的重复项目时才操作
            if !used_categories.contains(&category) && !replace_pool.is_empty() {
                // 取出重复类别中利润最小的项目（因为利润降序，末尾即最小）
                let min_repeated_profit = replace_pool.pop().unwrap();

                // 更新总利润：减去最小重复利润，加上当前项目利润
                total_profit += profit - min_repeated_profit;
                used_categories.insert(category);

                // 计算新的优雅度
                let distinct_count = used_categories.len() as i64;
                max_elegance = max_elegance.max(total_profit + distinct_count * distinct_count);
            }
        }

        max_elegance
    }
}
```
