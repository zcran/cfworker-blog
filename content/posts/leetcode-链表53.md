---
title: "leetcode-链表53"
date: 2026-06-29T10:55:36+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 堆盘子

堆盘子。设想有一堆盘子，堆太高可能会倒下来。因此，在现实生活中，盘子堆到一定高度时，我们就会另外堆一堆盘子。请实现数据结构SetOfStacks，模拟这种行为。SetOfStacks应该由多个栈组成，并且在前一个栈填满时新建一个栈。此外，SetOfStacks.push()和SetOfStacks.pop()应该与普通栈的操作方法相同（也就是说，pop()返回的值，应该跟只有一个栈时的情况一样）。 进阶：实现一个popAt(int index)方法，根据指定的子栈，执行pop操作。

当某个栈为空时，应当删除该栈。当栈中没有元素或不存在该栈时，pop，popAt 应返回 -1.


```
/// 堆盘子数据结构 - 由多个栈组成，前一个栈满时新建栈
///
/// # 特性
/// - push/pop 行为与普通栈一致
/// - popAt 支持从指定子栈弹出元素
/// - 空栈会被自动删除
/// - 操作失败返回 -1
struct StackOfPlates {
    stacks: Vec<Vec<i32>>,  // 多个子栈
    capacity: usize,        // 每个子栈的容量
}

impl StackOfPlates {
    /// 创建新的堆盘子结构
    ///
    /// # 参数
    /// - `cap`: 每个子栈的最大容量，若 <= 0 则所有操作无效
    pub fn new(cap: i32) -> Self {
        Self {
            stacks: Vec::new(),
            capacity: cap as usize,
        }
    }

    /// 向堆中压入盘子
    ///
    /// 如果当前栈已满或不存在，则创建新栈
    pub fn push(&mut self, plate: i32) {
        // 容量为 0 时不允许任何操作
        if self.capacity == 0 {
            return;
        }

        // 检查是否需要创建新栈：无栈 或 最后一个栈已满
        if self.stacks.is_empty()
            || self.stacks.last().unwrap().len() == self.capacity {
            self.stacks.push(Vec::with_capacity(self.capacity));
        }

        // 压入最后一个栈
        self.stacks.last_mut().unwrap().push(plate);
    }

    /// 从堆中弹出盘子（从最后一个栈弹出）
    ///
    /// # 返回
    /// 弹出的盘子值，若无盘子则返回 -1
    pub fn pop(&mut self) -> i32 {
        self.pop_at(self.stacks.len() as i32 - 1)
    }

    /// 从指定索引的子栈中弹出盘子
    ///
    /// # 参数
    /// - `index`: 子栈索引（从 0 开始）
    ///
    /// # 返回
    /// 弹出的盘子值，若栈不存在或为空则返回 -1
    pub fn pop_at(&mut self, index: i32) -> i32 {
        // 索引越界检查
        if index < 0 {
            return -1;
        }

        let idx = index as usize;
        if idx >= self.stacks.len() {
            return -1;
        }

        // 从指定栈弹出元素
        let plate = self.stacks[idx].pop();

        // 如果栈为空，删除该栈
        if self.stacks[idx].is_empty() {
            self.stacks.remove(idx);
        }

        // 返回弹出的值，若栈为空则返回 -1
        plate.unwrap_or(-1)
    }
}

// 可选：添加辅助方法便于调试
impl StackOfPlates {
    /// 获取总盘子数
    #[allow(dead_code)]
    pub fn total_count(&self) -> usize {
        self.stacks.iter().map(|s| s.len()).sum()
    }

    /// 获取子栈数量
    #[allow(dead_code)]
    pub fn stack_count(&self) -> usize {
        self.stacks.len()
    }
}
```
