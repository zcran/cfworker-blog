---
title: "leetcode-树状树组5"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 根据身高重建队列

假设有打乱顺序的一群人站成一个队列，数组 people 表示队列中一些人的属性（不一定按顺序）。每个 people[i] = [hi, ki] 表示第 i 个人的身高为 hi ，前面 正好 有 ki 个身高大于或等于 hi 的人。

请你重新构造并返回输入数组 people 所表示的队列。返回的队列应该格式化为数组 queue ，其中 queue[j] = [hj, kj] 是队列中第 j 个人的属性（queue[0] 是排在队列前面的人）。

```
impl Solution {
    /// 根据身高和前面人数重建队列
    /// 算法思路：
    /// 1. 将 people 按身高降序排序，身高相同则按 k（前面人数）升序排列
    /// 2. 依次将每个人插入到结果列表的索引 k 处（因为身高较高的人不会受后面插入的较矮者影响）
    /// 时间复杂度 O(n²)，空间复杂度 O(n)
    pub fn reconstruct_queue(people: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let mut people = people;
        // 排序：身高降序，k 值升序
        people.sort_by(|a, b| b[0].cmp(&a[0]).then(a[1].cmp(&b[1])));

        let len = people.len(); // 预先保存长度，避免移动后无法访问

        // 使用 fold 进行函数式累加：从空队列开始，每次将当前人插入到指定位置
        people.into_iter().fold(
            Vec::with_capacity(len), // 预分配容量，避免多次扩容
            |mut queue, person| {
                let insert_pos = person[1] as usize; // 插入位置即 person[1]（k）
                queue.insert(insert_pos, person);
                queue
            },
        )
    }
}
```
