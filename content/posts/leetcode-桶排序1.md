---
title: "Leetcode 桶排序1"
date: 2023-12-19T15:46:22+08:00
tags: ["leetcode", "桶排序"]
draft: false
---

## 存在重复元素 III

给你一个整数数组 nums 和两个整数 indexDiff 和 valueDiff 。

找出满足下述条件的下标对 (i, j)：

i != j,
abs(i - j) <= indexDiff
abs(nums[i] - nums[j]) <= valueDiff
如果存在，返回 true ；否则，返回 false 。

```
impl Solution {
    pub fn contains_nearby_almost_duplicate(
        nums: Vec<i32>,
        index_diff: i32,
        value_diff: i32,
    ) -> bool {
        // NOTE 桶排序的好题，通过将数字划分为若干个区间，来判断是否滑动窗口内是否存在value_diff范围内的数字对
        // 桶的大小为value_diff，作为整除的除数因此加一
        let value_diff = value_diff as i64 + 1;
        let mut num_bucket = std::collections::HashMap::new();

        for (i, &num) in nums.iter().enumerate() {
            let key = Self::get_id(num, value_diff);
            if num_bucket.contains_key(&key) {
                return true;
            }
            num_bucket.insert(key, num);

            // 检查相邻的区间有没有符合要求的数字
            if matches!(num_bucket.get(&(key-1)),Some(&prev_num)if (prev_num-num).abs()<value_diff as i32)
            {
                return true;
            }
            if matches!(num_bucket.get(&(key+1)),Some(&prev_num)if (prev_num-num).abs()<value_diff as i32)
            {
                return true;
            }

            if i >= index_diff as usize {
                num_bucket.remove(&Self::get_id(nums[i - index_diff as usize], value_diff));
            }
        }
        false
    }
    // NOTE 通过整除将数字划分到不同的桶中，为了避免溢出，这里value_diff是i64
    fn get_id(num: i32, value_diff: i64) -> i32 {
        let ret = if num >= 0 {
            num as i64 / value_diff
        } else {
            (num + 1) as i64 / value_diff - 1
        };
        ret as i32
    }
}
```