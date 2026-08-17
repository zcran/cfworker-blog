---
title: "leetcode-模拟25"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 分糖果 II

排排坐，分糖果。

我们买了一些糖果 candies，打算把它们分给排好队的 n = num_people 个小朋友。

给第一个小朋友 1 颗糖果，第二个小朋友 2 颗，依此类推，直到给最后一个小朋友 n 颗糖果。

然后，我们再回到队伍的起点，给第一个小朋友 n + 1 颗糖果，第二个小朋友 n + 2 颗，依此类推，直到给最后一个小朋友 2 * n 颗糖果。

重复上述过程（每次都比上一次多给出一颗糖果，当到达队伍终点后再次从队伍起点开始），直到我们分完所有的糖果。注意，就算我们手中的剩下糖果数不够（不比前一次发出的糖果多），这些糖果也会全部发给当前的小朋友。

返回一个长度为 num_people、元素之和为 candies 的数组，以表示糖果的最终分发情况（即 ans[i] 表示第 i 个小朋友分到的糖果数）。


```
impl Solution {
    /// 分糖果：第 k 轮给第 i 个小朋友 (k * n + i + 1) 颗糖果
    /// 时间复杂度: O(√candies)  空间复杂度: O(num_people)
    pub fn distribute_candies(candies: i32, num_people: i32) -> Vec<i32> {
        let n = num_people as i64;
        let mut c = candies as i64;
        let mut ans = vec![0i64; n as usize];
        let mut give = 1; // 当前应发的糖果数

        while c > 0 {
            let actual = c.min(give);
            ans[((give - 1) % n) as usize] += actual;
            c -= actual;
            give += 1;
        }

        ans.into_iter().map(|x| x as i32).collect()
    }
}
```
