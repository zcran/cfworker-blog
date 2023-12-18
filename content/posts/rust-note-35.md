---
title: "Rust Note 35"
date: 2023-06-10T19:51:15+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 买卖股票的最佳时机

给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。

你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

### C 解法：
```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int inf = 1e9;
        int minprice = inf, maxprofit = 0;
        for (int price: prices) {
            maxprofit = max(maxprofit, price - minprice);
            minprice = min(price, minprice);
        }
        return maxprofit;
    }
};
```


### Rust 解法：
```
impl Solution {
    pub fn max_profit(prices: Vec<i32>) -> i32 {
        let mut minmin = i32::MAX;
        let mut maxsell = 0;
        for price in prices.iter() {
            if *price < minmin {
                minmin = *price;
            }
            if price - minmin > maxsell {
                maxsell = price - minmin;
            }
        }
        return maxsell;
    }
}
```