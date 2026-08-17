---
title: "leetcode-模拟72"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 计算应缴税款总额

给你一个下标从 0 开始的二维整数数组 brackets ，其中 brackets[i] = [upperi, percenti] ，表示第 i 个税级的上限是 upperi ，征收的税率为 percenti 。税级按上限 从低到高排序（在满足 0 < i < brackets.length 的前提下，upperi-1 < upperi）。

税款计算方式如下：

· 不超过 upper0 的收入按税率 percent0 缴纳
· 接着 upper1 - upper0 的部分按税率 percent1 缴纳
· 然后 upper2 - upper1 的部分按税率 percent2 缴纳
· 以此类推

给你一个整数 income 表示你的总收入。返回你需要缴纳的税款总额。与标准答案误差不超 10-5 的结果将被视作正确答案。


```
impl Solution {
    /// 按税级逐级计算税款，每级仅对超出前一级上限的部分征税。
    pub fn calculate_tax(brackets: Vec<Vec<i32>>, income: i32) -> f64 {
        let (mut tax, mut prev) = (0.0, 0);
        for b in &brackets {
            let upper = b[0];
            let rate = b[1] as f64 / 100.0;
            if income >= upper {
                tax += (upper - prev) as f64 * rate;
                prev = upper;
            } else {
                tax += (income - prev).max(0) as f64 * rate;
                break;
            }
        }
        tax
    }
}
```
