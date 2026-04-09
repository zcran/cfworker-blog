---
title: "leetcode-数据流17"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 数字流的秩

假设你正在读取一串整数。每隔一段时间，你希望能找出数字 x 的秩(小于或等于 x 的值的个数)。请实现数据结构和算法来支持这些操作，也就是说：

实现 track(int x) 方法，每读入一个数字都会调用该方法；

实现 getRankOfNumber(int x) 方法，返回小于或等于 x 的值的个数。


```
struct StreamRank {
    data: Vec<i32>,
}

impl StreamRank {
    fn new() -> Self {
        Self { data: vec![] }
    }

    fn track(&mut self, x: i32) {
        let pos = self.data.partition_point(|&v| v <= x);
        self.data.insert(pos, x);
    }

    fn get_rank_of_number(&self, x: i32) -> i32 {
        self.data.partition_point(|&v| v <= x) as i32
    }
}
```






## `partition_point` 函数详解

`partition_point` 是 Rust 标准库提供给**有序切片**的方法，用于**二分查找分界点**。

### 函数签名

```rust
fn partition_point(&self, pred: F) -> usize
where
    F: Fn(&T) -> bool,
```

### 核心作用

找到**第一个不满足条件 `pred` 的元素索引**。

**关键前提**：切片必须关于 `pred` 是**分区的**（partitioned），即：
- 所有满足 `pred` 的元素在前半部分
- 所有不满足 `pred` 的元素在后半部分

---

## 工作原理图解

假设 `data = [1, 2, 3, 4, 5, 6, 7, 8]`，条件 `|&v| v <= 4`：

```
索引:     0    1    2    3    4    5    6    7
值:       1    2    3    4    5    6    7    8
条件:     ✓    ✓    ✓    ✓    ✗    ✗    ✗    ✗
         └─────┬─────┘         └────────┬────────┘
           满足条件                  不满足条件

partition_point = 4  ← 第一个不满足条件的索引
```

---

## 在 StreamRank 中的应用

```rust
fn get_rank_of_number(&self, x: i32) -> i32 {
    self.data.partition_point(|&v| v <= x) as i32
}
```

### 查找过程

当 `data = [1, 3, 5, 7]`，`x = 4`：

| 索引 | 值 | 条件 `v <= 4` | 说明 |
|------|----|--------------|------|
| 0 | 1 | ✓ 满足 | 继续 |
| 1 | 3 | ✓ 满足 | 继续 |
| 2 | 5 | ✗ 不满足 | **停止** |

**结果**：返回 `2`，表示有 2 个元素 `<= 4`

---

## 与手写二分的对比

### 手写二分版本
```rust
fn get_rank_of_number(&self, x: i32) -> i32 {
    let mut lo = 0;
    let mut hi = self.data.len();

    while lo < hi {
        let mid = lo + (hi - lo) / 2;
        if self.data[mid] <= x {
            lo = mid + 1;
        } else {
            hi = mid;
        }
    }

    lo as i32
}
```

### 使用 `partition_point`
```rust
fn get_rank_of_number(&self, x: i32) -> i32 {
    self.data.partition_point(|&v| v <= x) as i32
}
```

**优势**：
- ✅ 一行代码代替 10+ 行
- ✅ 意图更清晰（"找到分界点"）
- ✅ 避免手写二分容易出错（边界条件、死循环）
- ✅ 性能完全相同（都是 O(log n)）

---

## 更多 `partition_point` 示例

### 1. 找到第一个大于 10 的位置
```rust
let nums = vec![1, 3, 5, 7, 9, 11, 13, 15];
let idx = nums.partition_point(|&x| x <= 10);
println!("{}", idx);  // 输出: 5 (索引5的值是11)
```

### 2. 找到最后一个偶数的位置
```rust
let nums = vec![2, 4, 6, 8, 9, 11, 13];
let idx = nums.partition_point(|&x| x % 2 == 0);
println!("{}", idx);  // 输出: 4 (前4个都是偶数)
```

### 3. 字符串按长度分区
```rust
let words = vec!["a", "ab", "abc", "abcd", "abcde", "abcdef"];
let idx = words.partition_point(|&w| w.len() <= 3);
println!("{}", idx);  // 输出: 3 (前3个长度≤3)
```

---

## 使用 `partition_point` 的条件

**重要**：`partition_point` 要求切片**已经按条件分区**：

```rust
// ✓ 正确：所有 ≤4 的在前
let data = vec![1, 2, 3, 4, 5, 6];
let idx = data.partition_point(|&x| x <= 4);  // 返回 4

// ✗ 错误：分区混乱，结果不可预测
let data = vec![1, 5, 2, 6, 3, 4];
let idx = data.partition_point(|&x| x <= 4);  // 未定义行为！
```

在我们的 `StreamRank` 中，`data` 始终保持**有序**，所以满足分区条件。

---

## 与其他查找方法的对比

| 方法 | 用途 | 返回 | 复杂度 |
|------|------|------|--------|
| `binary_search` | 查找精确值 | `Result<usize, usize>` | O(log n) |
| `partition_point` | 查找分界点 | `usize` | O(log n) |
| `iter().position()` | 查找第一个满足条件 | `Option<usize>` | O(n) |

### 使用场景选择

```rust
let nums = vec![1, 3, 5, 7, 9];

// 1. 想知道是否包含 5
if let Ok(pos) = nums.binary_search(&5) {
    println!("找到于索引 {}", pos);
}

// 2. 想知道有多少个数 ≤ 6
let count = nums.partition_point(|&x| x <= 6);
println!("{} 个数 ≤ 6", count);  // 输出: 3

// 3. 查找第一个奇数（无序时）
let first_odd = nums.iter().position(|&x| x % 2 == 1);
```

---

## 总结

`partition_point` 是 Rust 提供的**二分查找工具**，专门用于：
- 在有序数组中查找**分界点**
- 统计满足条件的元素个数
- 找到第一个不满足条件的位置

在 `StreamRank` 中，它完美替代了手写二分查找，让代码更简洁、更安全、更易读。
