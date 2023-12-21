---
title: "Leetcode 概率与统计4"
date: 2023-12-19T15:47:55+08:00
tags: ["leetcode", "概率与统计"]
draft: false
---

## 新 21 点

爱丽丝参与一个大致基于纸牌游戏 “21点” 规则的游戏，描述如下：

爱丽丝以 0 分开始，并在她的得分少于 k 分时抽取数字。 抽取时，她从 [1, maxPts] 的范围中随机获得一个整数作为分数进行累计，其中 maxPts 是一个整数。 每次抽取都是独立的，其结果具有相同的概率。

当爱丽丝获得 k 分 或更多分 时，她就停止抽取数字。

爱丽丝的分数不超过 n 的概率是多少？

与实际答案误差不超过 10-5 的答案将被视为正确答案。

 
示例 1：

输入：n = 10, k = 1, maxPts = 10
输出：1.00000
解释：爱丽丝得到一张牌，然后停止。
示例 2：

输入：n = 6, k = 1, maxPts = 10
输出：0.60000
解释：爱丽丝得到一张牌，然后停止。 在 10 种可能性中的 6 种情况下，她的得分不超过 6 分。
示例 3：

输入：n = 21, k = 17, maxPts = 10
输出：0.73278
 

提示：

0 <= k <= n <= 104
1 <= maxPts <= 104

```
//这是一个简单的概率论问题。
//所以这道题考的是优化而非其他。
impl Solution {
pub fn new21_game(n: i32, k: i32, w: i32) -> f64 {
    let w=w as usize;
    let k=k as usize;
    let n=n as usize;
    if k+w<=n{
        1.0
    }else{
        let mut v:Vec<f64>=vec![0.;k+w+1];
        v[0]=0.;/*
        for i in (n+1..k+w).rev() {
            v[k]=0;//这是初始化步骤，由于我们其实已经初始化过了，所以这段代码可以省略
        }*/
        for i in k..n+1{//(k..n+1).rev()
            v[i]=1.;
        }
        let mut sum=(n-k) as f64;//do not compute v[k] here.
        for i in (1..k+1).rev(){
            sum+=v[i]-v[i+w];
//            let sum2:f64=v[i..i+w].iter().sum();
//            println!("k={:02}  {}:{},{}",i,sum2-sum,sum,sum2);
            v[i-1]=sum/w as f64;
        }
//        println!("{:?}",v);
        v[0]
    }
}
}
```