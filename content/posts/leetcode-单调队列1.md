---
title: "leetcode-单调队列1"
date: 2026-03-31T20:55:28+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 滑动窗口最大值

给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。

返回 滑动窗口中的最大值 。



示例 1：

输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
输出：[3,3,5,5,6,7]
解释：
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
示例 2：

输入：nums = [1], k = 1
输出：[1]


```
// 最终推荐版本
impl Solution {
    pub fn max_sliding_window(nums: Vec<i32>, k: i32) -> Vec<i32> {
        let k = k as usize;
        let mut deque = std::collections::VecDeque::new();

        nums.iter()
            .enumerate()
            .filter_map(|(i, &val)| {
                while let Some(&(_, idx)) = deque.front() {
                    if idx + k > i { break; }
                    deque.pop_front();
                }
                while let Some(&(v, _)) = deque.back() {
                    if v >= val { break; }
                    deque.pop_back();
                }
                deque.push_back((val, i));

                (i >= k - 1).then(|| deque.front().unwrap().0)
            })
            .collect()
    }
}

```


enumerate()：给每个元素配上索引 (i, &val)
结果：(0, &nums[0]), (1, &nums[1]), ...


filter_map：可以过滤（返回 None）或转换（返回 Some(T)）
只在窗口形成后（i >= k-1）返回 Some(最大值)，否则返回 None


· 第一部分：移除窗口外的元素

作用：删除已滑出窗口的索引

deque.front()：查看队首元素（最旧的可能过期的元素）
Some(&(_, idx))：解构元组，只关心索引
idx + k > i：判断是否仍在窗口内

窗口范围：[i-k+1, i]
元素在窗口内条件：idx >= i-k+1
等价于 idx + k > i
如果不在窗口内，pop_front() 移除
重复直到队首元素在窗口内



· 第二部分：维护单调递减队列

作用：保持队列递减，删除比当前值小的所有元素

deque.back()：查看队尾元素（最新的元素）
v >= val：如果队尾值大于等于当前值

保持递减顺序，停止删除
v < val：队尾值小于当前值

这个队尾元素永远不可能成为后续窗口的最大值（因为当前值更大且更新）
pop_back() 删除
关键思想：队列中只保留可能成为未来窗口最大值的候选元素


· 第三部分：添加当前元素

将当前元素及其索引入队
此时队列保持单调递减：队首最大，队尾最小


· 第四部分：产生结果

(i >= k - 1)：布尔值，判断窗口是否已满
.then(|| ...)：Rust 的布尔方法

如果条件为真，执行闭包返回 Some(...)
如果为假，返回 None
deque.front().unwrap().0：获取队首元素的值（最大值）

unwrap() 安全，因为窗口非空时队列也非空


· 最后
filter_map 产生的 Option<i32> 流
None 被自动过滤
Some(val) 被解包成 val
收集成 Vec<i32>
