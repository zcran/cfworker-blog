---
title: "leetcode-有序集合24"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 设计食物评分系统

设计一个支持下述操作的食物评分系统：

· 修改 系统中列出的某种食物的评分。
· 返回系统中某一类烹饪方式下评分最高的食物。

实现 FoodRatings 类：

· FoodRatings(String[] foods, String[] cuisines, int[] ratings) 初始化系统。食物由 foods、cuisines 和 ratings 描述，长度均为 n 。
    · foods[i] 是第 i 种食物的名字。
    · cuisines[i] 是第 i 种食物的烹饪方式。
    · ratings[i] 是第 i 种食物的最初评分。
· void changeRating(String food, int newRating) 修改名字为 food 的食物的评分。
· String highestRated(String cuisine) 返回指定烹饪方式 cuisine 下评分最高的食物的名字。如果存在并列，返回 字典序较小 的名字。

注意，字符串 x 的字典序比字符串 y 更小的前提是：x 在字典中出现的位置在 y 之前，也就是说，要么 x 是 y 的前缀，或者在满足 x[i] != y[i] 的第一个位置 i 处，x[i] 在字母表中出现的位置在 y[i] 之前。


```
use std::collections::{HashMap, BTreeSet};
use std::cmp::Reverse;

/// 食物评分系统
///
/// 支持修改食物评分和查询某类烹饪方式下评分最高的食物
///
/// # 时间复杂度
/// - new: O(n log n)
/// - changeRating: O(log n)
/// - highestRated: O(1)
///
/// # 空间复杂度
/// O(n)
pub struct FoodRatings {
    /// 食物 -> (评分, 烹饪方式)
    food_info: HashMap<String, (i32, String)>,
    /// 烹饪方式 -> 有序集合 (评分, 食物名)
    /// 使用 Reverse 实现降序排列：评分高的在前，评分相同字典序小的在前
    cuisine_rating: HashMap<String, BTreeSet<(Reverse<i32>, String)>>,
}

impl FoodRatings {
    /// 初始化食物评分系统
    ///
    /// # 参数
    /// - `foods`: 食物名称列表
    /// - `cuisines`: 烹饪方式列表
    /// - `ratings`: 评分列表
    pub fn new(foods: Vec<String>, cuisines: Vec<String>, ratings: Vec<i32>) -> Self {
        let mut food_info = HashMap::with_capacity(foods.len());
        let mut cuisine_rating: HashMap<String, BTreeSet<(Reverse<i32>, String)>> = HashMap::new();

        for ((food, cuisine), &rating) in foods.iter().zip(cuisines.iter()).zip(ratings.iter()) {
            // 存储食物信息
            food_info.insert(food.clone(), (rating, cuisine.clone()));

            // 将食物加入对应烹饪方式的集合
            // Reverse 实现评分降序，String 自动实现字典序升序
            cuisine_rating
                .entry(cuisine.clone())
                .or_insert_with(BTreeSet::new)
                .insert((Reverse(rating), food.clone()));
        }

        Self {
            food_info,
            cuisine_rating,
        }
    }

    /// 修改食物的评分
    ///
    /// # 参数
    /// - `food`: 食物名称
    /// - `new_rating`: 新评分
    pub fn change_rating(&mut self, food: String, new_rating: i32) {
        // 获取食物的旧评分和烹饪方式
        let (old_rating, cuisine) = self.food_info.get(&food)
            .expect("Food not found")
            .clone();

        // 如果评分没有变化，直接返回
        if old_rating == new_rating {
            return;
        }

        // 从旧集合中移除
        let set = self.cuisine_rating.get_mut(&cuisine).unwrap();
        set.remove(&(Reverse(old_rating), food.clone()));

        // 添加到新集合
        set.insert((Reverse(new_rating), food.clone()));

        // 更新食物信息
        self.food_info.insert(food, (new_rating, cuisine));
    }

    /// 返回指定烹饪方式下评分最高的食物
    ///
    /// # 参数
    /// - `cuisine`: 烹饪方式
    ///
    /// # 返回
    /// 评分最高的食物名称，如果评分相同返回字典序最小的
    pub fn highest_rated(&self, cuisine: String) -> String {
        self.cuisine_rating
            .get(&cuisine)
            .and_then(|set| set.iter().next())
            .map(|(_, food)| food.clone())
            .expect("Cuisine not found")
    }
}
```
