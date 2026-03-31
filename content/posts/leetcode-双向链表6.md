---
title: "Leetcode 双向链表6"
date: 2025-06-16T22:16:50+08:00
tags: ["leetcode", "双向链表"]
draft: false
---

## 设计前中后队列

请你设计一个队列，支持在前，中，后三个位置的 push 和 pop 操作。

请你完成 FrontMiddleBack 类：

FrontMiddleBack() 初始化队列。
void pushFront(int val) 将 val 添加到队列的 最前面 。
void pushMiddle(int val) 将 val 添加到队列的 正中间 。
void pushBack(int val) 将 val 添加到队里的 最后面 。
int popFront() 将 最前面 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 -1 。
int popMiddle() 将 正中间 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 -1 。
int popBack() 将 最后面 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 -1 。
请注意当有 两个 中间位置的时候，选择靠前面的位置进行操作。比方说：

将 6 添加到 [1, 2, 3, 4, 5] 的中间位置，结果数组为 [1, 2, 6, 3, 4, 5] 。
从 [1, 2, 3, 4, 5, 6] 的中间位置弹出元素，返回 3 ，数组变为 [1, 2, 4, 5, 6] 。



```

use std::collections::VecDeque;
struct FrontMiddleBackQueue {
    front_queue: VecDeque<i32>,
    back_queue: VecDeque<i32>,
}

impl FrontMiddleBackQueue {
    fn new() -> Self {
        Self {
            front_queue: Default::default(),
            back_queue: Default::default(),
        }
    }

    fn rebalance_queue(&mut self, front_longer: bool) {
        let front_len = self.front_queue.len();
        let back_len = self.back_queue.len();
        if front_len > back_len {// front 更长，需要移动元素到 back
            let move_len = (front_len - back_len + if front_longer { 0 } else { 1 }) / 2;
            if move_len != 0 {// 从 front 末尾取出元素，逆序插入到 back 开头
                for element in self.front_queue.drain(front_len - move_len..).rev() {
                    self.back_queue.push_front(element);
                }
            }
        } else {// back 更长或相等，需要移动元素到 front
            let move_len = (back_len - front_len + if front_longer { 1 } else { 0 }) / 2;
            if move_len != 0 { // 从 back 开头取出元素，添加到 front 末尾
                self.front_queue.extend(self.back_queue.drain(0..move_len));
            }
        }
    }

    // 直接插入到 front_queue 的前端，不需要平衡。
    fn push_front(&mut self, val: i32) {
        self.front_queue.push_front(val);
    }

    //插入前先平衡，然后插入到 front_queue 的末尾。
    fn push_middle(&mut self, val: i32) {
        self.rebalance_queue(false);

        self.front_queue.push_back(val);
    }

    //直接插入到 back_queue 的末尾。
    fn push_back(&mut self, val: i32) {
        self.back_queue.push_back(val);
    }

    //优先从 front_queue 前端删除，如果为空则从 back_queue 前端删除。
    fn pop_front(&mut self) -> i32 {
        self.front_queue
            .pop_front()
            .or_else(|| self.back_queue.pop_front())
            .unwrap_or(-1)
    }

    //先平衡，然后优先从 front_queue 末尾删除（中间位置），如果 front_queue 为空则从 back_queue 前端删除。
    fn pop_middle(&mut self) -> i32 {
        self.rebalance_queue(true);

        self.front_queue
            .pop_back()
            .or_else(|| self.back_queue.pop_front())
            .unwrap_or(-1)
    }

    // 优先从 back_queue 末尾删除，如果为空则从 front_queue 末尾删除。
    fn pop_back(&mut self) -> i32 {
        self.back_queue
            .pop_back()
            .or_else(|| self.front_queue.pop_back())
            .unwrap_or(-1)
    }
}
```



平衡规则：

正常情况下：front_len = back_len 或 front_len = back_len + 1
当需要操作中间元素时，可能暂时打破平衡，调用此方法恢复
front_longer 参数：

true：倾向于让 front 更长（用于 pop_middle 后）
false：倾向于让两者平衡（用于 push_middle 前）


时间复杂度

所有操作：O(1) 均摊时间（rebalance 可能移动元素，但每个元素只移动有限次数）
空间复杂度：O(n)
应用场景

这个数据结构常用于需要频繁在多个位置插入删除的场景，如：

任务调度器（支持优先级调整）
播放列表管理
