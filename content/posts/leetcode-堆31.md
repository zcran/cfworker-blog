---
title: "leetcode-堆31"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最大平均通过率

一所学校里有一些班级，每个班级里有一些学生，现在每个班都会进行一场期末考试。给你一个二维数组 classes ，其中 classes[i] = [passˇi, totalˇi] ，表示你提前知道了第 i 个班级总共有 totalˇi 个学生，其中只有 passˇi 个学生可以通过考试。

给你一个整数 extraStudents ，表示额外有 extraStudents 个聪明的学生，他们 一定 能通过任何班级的期末考。你需要给这 extraStudents 个学生每人都安排一个班级，使得 所有 班级的 平均 通过率 最大 。

一个班级的 通过率 等于这个班级通过考试的学生人数除以这个班级的总人数。平均通过率 是所有班级的通过率之和除以班级数目。

请你返回在安排这 extraStudents 个学生去对应班级后的 最大 平均通过率。与标准答案误差范围在 10^-5 以内的结果都会视为正确结果。


```
use std::collections::BinaryHeap;
use std::cmp::Ordering;

#[derive(Debug)]
struct Class {
    pass: i64,
    total: i64,
}

impl Class {
    /// Gain from adding one student: (total - pass) / (total * (total + 1))
    fn gain(&self) -> (i64, i64) {
        (self.total - self.pass, self.total * (self.total + 1))
    }
}

impl PartialEq for Class {
    fn eq(&self, other: &Self) -> bool {
        self.gain() == other.gain()
    }
}
impl Eq for Class {}

impl PartialOrd for Class {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

impl Ord for Class {
    fn cmp(&self, other: &Self) -> Ordering {
        let (a_num, a_den) = self.gain();
        let (b_num, b_den) = other.gain();
        // Compare a_num/a_den vs b_num/b_den by cross multiplication
        (a_num * b_den).cmp(&(b_num * a_den))
    }
}

impl Solution {
    pub fn max_average_ratio(classes: Vec<Vec<i32>>, extra_students: i32) -> f64 {
        let mut heap: BinaryHeap<Class> = classes
            .iter()
            .map(|c| Class { pass: c[0] as i64, total: c[1] as i64 })
            .collect();

        for _ in 0..extra_students {
            let mut c = heap.pop().unwrap();
            c.pass += 1;
            c.total += 1;
            heap.push(c);
        }

        let n = classes.len() as f64;
        heap.iter().map(|c| c.pass as f64 / c.total as f64).sum::<f64>() / n
    }
}
```
