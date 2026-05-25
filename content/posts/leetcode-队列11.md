---
title: "leetcode-队列11"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 无法吃午餐的学生数量


学校的自助午餐提供圆形和方形的三明治，分别用数字 0 和 1 表示。所有学生站在一个队列里，每个学生要么喜欢圆形的要么喜欢方形的。
餐厅里三明治的数量与学生的数量相同。所有三明治都放在一个 栈 里，每一轮：

· 如果队列最前面的学生 喜欢 栈顶的三明治，那么会 拿走它 并离开队列。
· 否则，这名学生会 放弃这个三明治 并回到队列的尾部。

这个过程会一直持续到队列里所有学生都不喜欢栈顶的三明治为止。

给你两个整数数组 students 和 sandwiches ，其中 sandwiches[i] 是栈里面第 i 个三明治的类型（i = 0 是栈的顶部）， students[j] 是初始队列里第 j 名学生对三明治的喜好（j = 0 是队列的最开始位置）。请你返回无法吃午餐的学生数量。


```
impl Solution {
    /// 统计无法吃午餐的学生数量
    /// 算法：统计喜欢圆形（0）和方形（1）三明治的学生数量，
    /// 然后按顺序遍历三明治，若当前三明治类型还有学生喜欢，则消耗一个学生，否则停止。
    pub fn count_students(students: Vec<i32>, sandwiches: Vec<i32>) -> i32 {
        let n = students.len();
        let mut cnt = [0, 0];  // cnt[0] 喜欢圆形，cnt[1] 喜欢方形
        for &s in &students {
            cnt[s as usize] += 1;
        }

        let mut served = 0;
        for &sandwich in &sandwiches {
            if cnt[sandwich as usize] == 0 {
                break;  // 无人喜欢当前三明治，剩余学生无法就餐
            }
            cnt[sandwich as usize] -= 1;
            served += 1;
        }

        (n - served) as i32
    }
}
```
