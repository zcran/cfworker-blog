---
title: "leetcode-栈61"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 验证图书取出顺序

现在图书馆有一堆图书需要放入书架，并且图书馆的书架是一种特殊的数据结构，只能按照 一定 的顺序 放入 和 拿取 书籍。

给定一个表示图书放入顺序的整数序列 putIn，请判断序列 takeOut 是否为按照正确的顺序拿取书籍的操作序列。你可以假设放入书架的所有书籍编号都不相同。


```
impl Solution {
    /// 验证 takeOut 是否为 putIn 对应的合法出栈序列
    ///
    /// # 算法思路
    /// 使用栈模拟入栈和出栈过程：
    /// 1. 遍历出栈序列 popped，对于每个待出栈元素 p：
    ///    - 如果栈顶不等于 p，则持续从 putIn 中取元素入栈，直到栈顶等于 p
    ///    - 如果 putIn 已取完仍未找到 p，则序列非法
    /// 2. 栈顶等于 p 时，弹出栈顶
    /// 3. 所有元素处理完后，如果栈为空则序列合法
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)，每个元素最多入栈和出栈一次
    /// - 空间复杂度：O(n)，栈空间
    pub fn validate_book_sequences(put_in: Vec<i32>, take_out: Vec<i32>) -> bool {
        let mut stack = Vec::with_capacity(put_in.len());
        let mut push_index = 0;

        for &target in &take_out {
            // 如果栈顶不是目标元素，从 put_in 中继续入栈
            while stack.last() != Some(&target) {
                // 如果 put_in 已全部入栈，说明找不到目标元素，序列非法
                if push_index >= put_in.len() {
                    return false;
                }
                stack.push(put_in[push_index]);
                push_index += 1;
            }

            // 栈顶等于目标元素，弹出
            stack.pop();
        }

        // 所有出栈操作完成，序列合法
        true
    }
}
```
