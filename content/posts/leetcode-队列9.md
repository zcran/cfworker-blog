---
title: "leetcode-队列9"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 按递增顺序显示卡牌

牌组中的每张卡牌都对应有一个唯一的整数。你可以按你想要的顺序对这套卡片进行排序。

最初，这些卡牌在牌组里是正面朝下的（即，未显示状态）。

现在，重复执行以下步骤，直到显示所有卡牌为止：

1.从牌组顶部抽一张牌，显示它，然后将其从牌组中移出。
2.如果牌组中仍有牌，则将下一张处于牌组顶部的牌放在牌组的底部。
3.如果仍有未显示的牌，那么返回步骤 1。否则，停止行动。


返回能以递增顺序显示卡牌的牌组顺序。

答案中的第一张牌被认为处于牌堆顶部。


```
use std::collections::VecDeque;

impl Solution {
    /// 返回重新排列后的牌组顺序，使得模拟的“抽牌过程”得到递增序列。
    /// 算法：将牌排序后，从大到小依次插入队列头部，每次插入前将队尾元素移到队首。
    pub fn deck_revealed_increasing(mut deck: Vec<i32>) -> Vec<i32> {
        deck.sort_unstable();                     // 升序排序
        let n = deck.len();
        let mut queue = VecDeque::with_capacity(n); // 预分配，避免多次扩容

        // 从最大牌开始处理
        for &card in deck.iter().rev() {
            if !queue.is_empty() {
                let bottom = queue.pop_back().unwrap();
                queue.push_front(bottom);
            }
            queue.push_front(card);
        }

        queue.into_iter().collect()
    }
}
```
