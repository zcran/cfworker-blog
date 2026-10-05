---
title: "leetcode-贪心185"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 心算挑战

「力扣挑战赛」心算项目的挑战比赛中，要求选手从 N 张卡牌中选出 cnt 张卡牌，若这 cnt 张卡牌数字总和为偶数，则选手成绩「有效」且得分为 cnt 张卡牌数字总和。 给定数组 cards 和 cnt，其中 cards[i] 表示第 i 张卡牌上的数字。 请帮参赛选手计算最大的有效得分。若不存在获取有效得分的卡牌方案，则返回 0。


```
impl Solution {
    pub fn maximum_score(cards: Vec<i32>, cnt: i32) -> i32 {
        // 1. 降序排序，取最大的 cnt 张牌，求和 sum。
        //    同时记录已选牌中最小的奇数 odd_min 和最小的偶数 even_min。
        // 2. 若 sum 为偶数，直接就是答案。
        // 3. 若 sum 为奇数，需要调整：用「未选牌中最大的偶数」换掉「已选牌中最小的奇数」，
        //    或用「未选牌中最大的奇数」换掉「已选牌中最小的偶数」，
        //    使总和变为偶数。取两种方案中较大的结果。
        let mut cards = cards;
        cards.sort_unstable_by(|a, b| b.cmp(a));

        let cnt = cnt as usize;
        let mut sum: i32 = 0;
        let mut odd_min = i32::MAX;   // 已选牌中最小的奇数
        let mut even_min = i32::MAX;  // 已选牌中最小的偶数
        for &x in &cards[..cnt] {
            sum += x;
            if x & 1 == 1 {
                odd_min = odd_min.min(x);
            } else {
                even_min = even_min.min(x);
            }
        }

        // 已选之和为偶数，直接返回
        if sum & 1 == 0 {
            return sum;
        }

        // 在未选牌中找最大的奇数和最大的偶数
        let mut odd_max = i32::MIN;
        let mut even_max = i32::MIN;
        for &x in &cards[cnt..] {
            if x & 1 == 1 {
                odd_max = odd_max.max(x);
            } else {
                even_max = even_max.max(x);
            }
        }

        // 两种修正方案，取较大者
        let mut ans = 0;
        // 方案 A：换掉已选的最小奇数，补入未选的最大偶数
        if odd_min != i32::MAX && even_max != i32::MIN {
            ans = ans.max(sum - odd_min + even_max);
        }
        // 方案 B：换掉已选的最小偶数，补入未选的最大奇数
        if even_min != i32::MAX && odd_max != i32::MIN {
            ans = ans.max(sum - even_min + odd_max);
        }

        ans
    }
}
```
