---
title: "leetcode-拓扑排序14"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 从给定原材料中找到所有可以做出的菜

你有 n 道不同菜的信息。给你一个字符串数组 recipes 和一个二维字符串数组 ingredients 。第 i 道菜的名字为 recipes[i] ，如果你有它 所有 的原材料 ingredients[i] ，那么你可以 做出 这道菜。一份食谱也可以是 其它 食谱的原料，也就是说 ingredients[i] 可能包含 recipes 中另一个字符串。

同时给你一个字符串数组 supplies ，它包含你初始时拥有的所有原材料，每一种原材料你都有无限多。

请你返回你可以做出的所有菜。你可以以 任意顺序 返回它们。

注意两道菜在它们的原材料中可能互相包含。

```
use std::collections::{HashMap, HashSet};

impl Solution {
    /// 返回所有可以制作的食谱列表（LeetCode 2115）。
    /// 给定食谱 `recipes`、每个食谱所需的原料 `ingredients` 和已有的供应 `supplies`，
    /// 判断哪些食谱可以被制作（所需原料要么在 `supplies` 中，要么可以被其他食谱制作）。
    pub fn find_all_recipes(
        recipes: Vec<String>,
        ingredients: Vec<Vec<String>>,
        supplies: Vec<String>,
    ) -> Vec<String> {
        // 将 supplies 转为 HashSet 以便 O(1) 查询，存储引用避免克隆
        let supply_set: HashSet<_> = supplies.iter().collect();

        // 建立食谱到其原料列表的映射（食谱引用 -> 原料列表引用）
        let recipe_map: HashMap<_, _> = recipes
            .iter()
            .zip(ingredients.iter())
            .collect();

        // 记忆化结果：每个食谱是否可制作（None 表示未计算）
        let mut memo: HashMap<&String, bool> = HashMap::new();

        // 对每个食谱进行 DFS 判断，收集可制作的食谱
        recipes
            .iter()
            .filter(|&recipe| dfs(recipe, &recipe_map, &mut memo, &supply_set))
            .cloned()
            .collect()
    }
}

/// 深度优先搜索判断一个食谱是否可制作（带记忆化）。
///
/// # 生命周期
/// - `'a`：所有引用都来自传入的 `recipes` 和 `supplies` 数据，保证在函数执行期间有效。
///
/// # 参数
/// - `recipe`: 当前要判断的食谱名称（引用）
/// - `recipe_map`: 食谱到其原料列表的映射，键和值均为引用
/// - `memo`: 记忆化结果缓存
/// - `supply_set`: 已有供应的集合（存储字符串引用）
fn dfs<'a>(
    recipe: &'a String,
    recipe_map: &'a HashMap<&'a String, &'a Vec<String>>,
    memo: &mut HashMap<&'a String, bool>,
    supply_set: &'a HashSet<&String>,
) -> bool {
    // 如果已经计算过，直接返回缓存结果
    if let Some(&res) = memo.get(recipe) {
        return res;
    }

    // 临时标记为 false，防止循环依赖（若出现循环，则会返回 false）
    memo.insert(recipe, false);

    // 获取当前食谱的原料列表
    let ing_list = match recipe_map.get(recipe) {
        Some(list) => list,
        None => return false, // 未知食谱（不存在于映射中）视为无法制作
    };

    // 检查所有原料是否都可获得
    let all_available = ing_list.iter().all(|ingredient| {
        if supply_set.contains(ingredient) {
            // 原料直接存在于供应中
            true
        } else if let Some(&is_makeable) = memo.get(ingredient) {
            // 已经判断过该原料是否为可制作食谱
            is_makeable
        } else if recipe_map.contains_key(ingredient) {
            // 原料本身是一个食谱，递归判断
            dfs(ingredient, recipe_map, memo, supply_set)
        } else {
            // 原料既不是供应也不是已知食谱
            false
        }
    });

    let result = all_available;
    memo.insert(recipe, result);
    result
}
```
