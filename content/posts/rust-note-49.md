---
title: "Rust Note 49"
date: 2023-06-12T10:35:06+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 缺失数字

给定一个包含 [0, n] 中 n 个数的数组 nums ，找出 [0, n] 这个范围内没有出现在数组中的那个数。

### C 解法：
```
  // 位运算
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int res = 0;
        int n = nums.size();
        for (int i = 0; i < n; i++) {
            res ^= nums[i];
        }
        for (int i = 0; i <= n; i++) {
            res ^= i;
        }
        return res;
    }
};
```


### Rust 解法：
```
impl Solution {
    pub fn missing_number(nums: Vec<i32>) -> i32 {
        [nums.len(), 1, nums.len() + 1, 0][nums.len() & 3] as i32 
          ^ nums
            .into_iter()
            .fold(0, |o, n| o ^ n)
    }
}
```