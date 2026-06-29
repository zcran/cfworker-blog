---
title: "leetcode-有序集合31"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 设计任务管理器

一个任务管理器系统可以让用户管理他们的任务，每个任务有一个优先级。这个系统需要高效地处理添加、修改、执行和删除任务的操作。

请你设计一个 TaskManager 类：

· TaskManager(vector<vector<int>>& tasks) 初始化任务管理器，初始化的数组格式为 [userId, taskId, priority] ，表示给 userId 添加一个优先级为 priority 的任务 taskId 。

· void add(int userId, int taskId, int priority) 表示给用户 userId 添加一个优先级为 priority 的任务 taskId ，输入 保证 taskId 不在系统中。

· void edit(int taskId, int newPriority) 更新已经存在的任务 taskId 的优先级为 newPriority 。输入 保证 taskId 存在于系统中。

· void rmv(int taskId) 从系统中删除任务 taskId 。输入 保证 taskId 存在于系统中。

· int execTop() 执行所有用户的任务中优先级 最高 的任务，如果有多个任务优先级相同且都为 最高 ，执行 taskId 最大的一个任务。执行完任务后，taskId 从系统中 删除 。同时请你返回这个任务所属的用户 userId 。如果不存在任何任务，返回 -1 。

注意 ，一个用户可能被安排多个任务。


```
use std::collections::{BinaryHeap, HashMap};
use std::cmp::Ordering;

// 堆中存储的任务：优先级高、任务ID大的排在前面
#[derive(Eq, PartialEq)]
struct HeapTask {
    priority: i32,
    task_id: i32,
}

impl Ord for HeapTask {
    fn cmp(&self, other: &Self) -> Ordering {
        self.priority
            .cmp(&other.priority)
            .then_with(|| self.task_id.cmp(&other.task_id))
    }
}

impl PartialOrd for HeapTask {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

pub struct TaskManager {
    // task_id -> (priority, user_id)
    tasks: HashMap<i32, (i32, i32)>,
    heap: BinaryHeap<HeapTask>,
}

impl TaskManager {
    pub fn new(tasks: Vec<Vec<i32>>) -> Self {
        let mut manager = Self {
            tasks: HashMap::with_capacity(tasks.len()),
            heap: BinaryHeap::with_capacity(tasks.len()),
        };

        for t in tasks {
            manager.add(t[0], t[1], t[2]);
        }

        manager
    }

    pub fn add(&mut self, user_id: i32, task_id: i32, priority: i32) {
        self.tasks.insert(task_id, (priority, user_id));
        self.heap.push(HeapTask { priority, task_id });
    }

    pub fn edit(&mut self, task_id: i32, new_priority: i32) {
        // 更新哈希表，并推入新的优先级到堆中（旧记录作为脏数据延迟清理）
        if let Some(info) = self.tasks.get_mut(&task_id) {
            info.0 = new_priority;
            self.heap.push(HeapTask { priority: new_priority, task_id });
        }
    }

    pub fn rmv(&mut self, task_id: i32) {
        self.tasks.remove(&task_id);
    }

    pub fn exec_top(&mut self) -> i32 {
        // 惰性清理：跳过无效的堆顶元素
        while let Some(top) = self.heap.pop() {
            if let Some(&(priority, user_id)) = self.tasks.get(&top.task_id) {
                if priority == top.priority {
                    // 执行任务：从系统中删除
                    self.tasks.remove(&top.task_id);
                    return user_id;
                }
                // 如果优先级不匹配，说明是旧记录，继续弹出
            }
            // 任务已被删除，继续弹出
        }
        -1
    }
}
```
