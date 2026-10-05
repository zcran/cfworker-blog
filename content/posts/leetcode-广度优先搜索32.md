---
title: "leetcode-广度优先搜索32"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 通知所有员工所需的时间

公司里有 n 名员工，每个员工的 ID 都是独一无二的，编号从 0 到 n - 1。公司的总负责人通过 headID 进行标识。

在 manager 数组中，每个员工都有一个直属负责人，其中 manager[i] 是第 i 名员工的直属负责人。对于总负责人，manager[headID] = -1。题目保证从属关系可以用树结构显示。

公司总负责人想要向公司所有员工通告一条紧急消息。他将会首先通知他的直属下属们，然后由这些下属通知他们的下属，直到所有的员工都得知这条紧急消息。

第 i 名员工需要 informTime[i] 分钟来通知它的所有直属下属（也就是说在 informTime[i] 分钟后，他的所有直属下属都可以开始传播这一消息）。

返回通知所有员工这一紧急消息所需要的 分钟数 。


```
impl Solution {
    pub fn num_of_minutes(n: i32, head_id: i32, manager: Vec<i32>, inform_time: Vec<i32>) -> i32 {
        // 自底向上累加：dfs(i) 表示从员工 i 开始把消息传遍其子树所需的时间
        // 记忆化：把 manager[i] 置为 -1 表示已经计算过，避免重复递归
        fn dfs(i: usize, manager: &mut Vec<i32>, inform_time: &mut Vec<i32>) -> i32 {
            if manager[i] != -1 {
                let parent = manager[i] as usize;
                inform_time[i] += dfs(parent, manager, inform_time);
                manager[i] = -1; // 标记已计算
            }
            inform_time[i]
        }

        let mut manager = manager;
        let mut inform_time = inform_time;

        // 对所有员工取最大值，即为整棵树传播完成所需时间
        (0..n as usize)
            .map(|i| dfs(i, &mut manager, &mut inform_time))
            .max()
            .unwrap_or(0)
    }
}
```
