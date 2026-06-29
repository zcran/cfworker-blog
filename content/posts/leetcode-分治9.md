---
title: "leetcode-分治9"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 数组中的第K个最大元素

给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。

请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

你必须设计并实现时间复杂度为 O(n) 的算法解决此问题。


```
impl Solution {
    pub fn find_kth_largest(mut nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let (_, &mut kth, _) = nums.select_nth_unstable(n - k as usize);
        kth
    }
}
```


`select_nth_unstable` 是 Rust 标准库为切片（slice）提供的一个**选择算法**（Selection Algorithm）。

简单来说，它会重新排列切片的元素，使得指定索引位置的元素，恰好处于**整个切片排完序后它应该在的位置**。同时，它还能保证这个元素左边的元素都小于等于它，右边的元素都大于等于它。

它的完整签名如下，虽然返回值是一个元组，但用法非常直观：

```rust
pub fn select_nth_unstable(&mut self, index: usize) -> (&mut [T], &mut T, &mut [T])
```

-   **`index`**：你想要找到的“第 n 个”元素的位置（从 0 开始计数）。
-   **返回值**：一个包含三个部分的元组：
    1.  **左子切片**：包含所有小于或等于中间元素的元素。这部分是**未排序**的，但其中的所有元素都 ≤ 中间元素。
    2.  **中间元素的可变引用**：这正是你想要的第 `index` 个元素，它已经处于最终的正确位置上。
    3.  **右子切片**：包含所有大于或等于中间元素的元素。这部分也是**未排序**的，但其中的所有元素都 ≥ 中间元素。

#### 代码示例

```rust
// 示例数据
let mut numbers = [3, 1, 5, 2, 4];

// 我们想找到第 2 个元素（从0开始计数），也就是第三小的数
let index = 2;

// 调用函数
let (left, pivot, right) = numbers.select_nth_unstable(index);

// 输出结果
println!("左子切片（未排序）: {:?}", left);   // 输出可能是 [1, 2]
println!("中间元素（第 {} 位的值）: {}", index, *pivot); // 输出 3
println!("右子切片（未排序）: {:?}", right);  // 输出可能是 [5, 4]

// 验证：所有左子切片的元素都 <= pivot
assert!(left.iter().all(|&x| x <= *pivot));
// 验证：所有右子切片的元素都 >= pivot
assert!(right.iter().all(|&x| x >= *pivot));
```

如你所见，虽然 `left` 和 `right` 内部还是乱的，但我们已经成功找到了第 `index` 大的元素。

### ⚙️ 算法实现：Introselect

这个函数之所以强大，是因为它内部实现了一个叫做 **Introselect** 的算法。你可以把它理解为一个“性能怪兽”，结合了两种算法的优点。

1.  **核心：基于快速排序的 Quickselect**
    算法会像快速排序一样，选择一个“基准”，然后通过划分子数组的方式来不断缩小查找范围，这是它平均性能极佳（O(n)）的原因。

2.  **保底：中位数的中位数（Median of Medians）**
    这是它的“安全网”。在极其罕见的情况下，如果 `select_nth_unstable` 运气不好，连续多次都选到了很差的基准，导致效率下降，它就会自动切换到一个更稳健的算法——**中位数的中位数**。这个算法能保证在最坏情况下也能在线性时间 O(n) 内完成，虽然常数较大，但彻底杜绝了性能暴跌的可能。

在你的找第 `k` 大数的代码中，`select_nth_unstable(n - k as usize)` 正是利用了这个特性，以极高的效率找出了第 `k` 大的元素，实现了时间复杂度和编码难度的完美平衡。
