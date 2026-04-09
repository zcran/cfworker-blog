---
title: "leetcode-单调队列9"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 你可以安排的最多任务数目

给你 n 个任务和 m 个工人。每个任务需要一定的力量值才能完成，需要的力量值保存在下标从 0 开始的整数数组 tasks 中，第 i 个任务需要 tasks[i] 的力量才能完成。每个工人的力量值保存在下标从 0 开始的整数数组 workers 中，第 j 个工人的力量值为 workers[j] 。每个工人只能完成 一个 任务，且力量值需要 大于等于 该任务的力量要求值（即 workers[j] >= tasks[i] ）。

除此以外，你还有 pills 个神奇药丸，可以给 一个工人的力量值 增加 strength 。你可以决定给哪些工人使用药丸，但每个工人 最多 只能使用 一片 药丸。

给你下标从 0 开始的整数数组tasks 和 workers 以及两个整数 pills 和 strength ，请你返回 最多 有多少个任务可以被完成。


```

impl Solution {
    pub fn max_task_assign(mut tasks: Vec<i32>, mut workers: Vec<i32>, pills: i32, strength: i32) -> i32 {
        let n = tasks.len();
        let m = workers.len();
        let mut tasks = tasks.to_vec();
        let mut workers = workers.to_vec();
        tasks.sort_unstable();
        workers.sort_unstable();

        let check = |c: usize| -> bool {
            let mut q: VecDeque<i32> = tasks.iter().take(c).copied().collect();
            let mut t = 0;
            for &worker in workers.iter().rev().take(c).rev() {
                if q[0] <= worker {
                    q.pop_front();
                    continue;
                }
                t += 1;
                if t > pills {
                    return false;
                }
                let up = worker + strength;
                // 找到小于或等于 up 的最大元素
                match q.iter().rposition(|&x| x <= up) {
                    Some(i) => {
                        q.remove(i);
                    },
                    None => return false,
                }
            }
            q.is_empty()
        };

        let (mut l, mut r) = (0, n.min(m));
        while l < r {
            let mid = (l + r + 1) >> 1;
            if check(mid) {
                l = mid;
            } else {
                r = mid - 1;
            }
        }
        l as i32
    }
}
```
