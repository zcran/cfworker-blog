---
title: "leetcode-分治40"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 库存管理 II

仓库管理员以数组 stock 形式记录商品库存表。stock[i] 表示商品 id，可能存在重复。请返回库存表中数量大于 stock.length / 2 的商品 id。


```
impl Solution {
    /// 使用 Boyer-Moore 多数投票算法查找数组中的多数元素
    ///
    /// # 算法原理
    /// Boyer-Moore 投票算法：
    /// 1. 维护一个候选元素和计数器
    /// 2. 遍历数组，如果计数器为0，将当前元素设为候选
    /// 3. 如果当前元素等于候选，计数器加1，否则减1
    /// 4. 最后剩下的候选元素即为多数元素
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)
    /// - 空间复杂度：O(1)
    ///
    /// # 参数
    /// - `nums`: 整数数组，保证存在多数元素
    ///
    /// # 返回
    /// 多数元素的值
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// assert_eq!(Solution::majority_element(vec![3, 2, 3]), 3);
    /// assert_eq!(Solution::majority_element(vec![2, 2, 1, 1, 1, 2, 2]), 2);
    /// ```
    pub fn inventory_management(nums: Vec<i32>) -> i32 {
        let mut candidate = 0;
        let mut count = 0;

        for &num in nums.iter() {
            if count == 0 {
                candidate = num;
            }

            if num == candidate {
                count += 1;
            } else {
                count -= 1;
            }
        }

        candidate
    }
}
```
