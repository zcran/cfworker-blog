---
title: "leetcode-有序集合38"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 设计事件管理器

给你一组初始事件列表，其中每个事件有一个唯一的 eventId 和一个 priority（优先级）。

实现 EventManager 类：

· EventManager(int[][] events) 使用给定事件初始化管理器，其中 events[i] = [eventIdi, priorityi]。
· void updatePriority(int eventId, int newPriority) 更新具有 id 为 eventId 的 活跃 事件的优先级为 newPriority。
· int pollHighest() 移除并返回具有 最高优先级 的 活跃事件 的 eventId。如果有多个活动事件具有相同的优先级，则返回 eventId 最小的事件。如果没有活跃事件，则返回 -1。

如果一个事件没有被 pollHighest() 移除，则称其为 活跃事件。


```
use std::collections::HashMap;
use std::cmp::Reverse;

/// 事件管理器，支持更新优先级和获取最高优先级事件
struct EventManager {
    /// 事件ID -> 当前优先级 (如果事件已被移除，优先级为 -1 或不存在)
    id_to_priority: HashMap<i32, i32>,
    /// 最大堆: (优先级, 事件ID取负) 实现按优先级降序、ID升序排列
    heap: std::collections::BinaryHeap<(i32, Reverse<i32>)>,
}

impl EventManager {
    /// 使用初始事件列表初始化管理器
    fn new(events: Vec<Vec<i32>>) -> Self {
        let mut id_to_priority = HashMap::with_capacity(events.len());
        let mut heap = std::collections::BinaryHeap::with_capacity(events.len());

        for event in events {
            let id = event[0];
            let priority = event[1];
            id_to_priority.insert(id, priority);
            // 使用 Reverse 实现 ID 升序 (因为 BinaryHeap 默认是大根堆)
            heap.push((priority, Reverse(id)));
        }

        Self { id_to_priority, heap }
    }

    /// 更新活跃事件的优先级
    fn update_priority(&mut self, event_id: i32, new_priority: i32) {
        // 更新映射
        self.id_to_priority.insert(event_id, new_priority);
        // 插入新的堆条目 (旧条目会在 poll 时被惰性删除)
        self.heap.push((new_priority, Reverse(event_id)));
    }

    /// 移除并返回最高优先级事件的 ID，如果没有活跃事件则返回 -1
    fn poll_highest(&mut self) -> i32 {
        while let Some((priority, Reverse(id))) = self.heap.pop() {
            // 检查这个条目是否仍然有效
            if let Some(&current_priority) = self.id_to_priority.get(&id) {
                if current_priority == priority {
                    // 有效条目：移除事件
                    self.id_to_priority.remove(&id);
                    return id;
                }
                // 否则这是过期条目，继续弹出
            }
            // 如果 ID 不在映射中，也是过期条目
        }
        -1
    }
}
```
