---
title: "leetcode-模拟101"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 最小数字游戏

你有一个下标从 0 开始、长度为 偶数 的整数数组 nums ，同时还有一个空数组 arr 。Alice 和 Bob 决定玩一个游戏，游戏中每一轮 Alice 和 Bob 都会各自执行一次操作。游戏规则如下：

· 每一轮，Alice 先从 nums 中移除一个 最小 元素，然后 Bob 执行同样的操作。
· 接着，Bob 会将移除的元素添加到数组 arr 中，然后 Alice 也执行同样的操作。
· 游戏持续进行，直到 nums 变为空。

返回结果数组 arr 。


```
impl Solution {
  pub fn number_game(mut nums: Vec<i32>) -> Vec<i32> {
      nums.sort_unstable();
      // 排序后每轮 Alice 取前一个，Bob 取后一个；
      // 但 Bob 先放入 arr，所以每对要交换顺序
      for chunk in nums.chunks_exact_mut(2) {
          chunk.swap(0, 1);
      }
      nums
  }
}
```
