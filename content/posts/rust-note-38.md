---
title: "Rust Note 38"
date: 2023-06-10T19:51:15+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 打乱数组

给你一个整数数组 nums ，设计算法来打乱一个没有重复元素的数组。打乱后，数组的所有排列应该是 等可能 的。

实现 Solution class:

Solution(int[] nums) 使用整数数组 nums 初始化对象
int[] reset() 重设数组到它的初始状态并返回
int[] shuffle() 返回数组随机打乱后的结果

### C 解法：
```
// 原地乱序，Fisher-Yates 洗牌算法
class Solution {
public:
    Solution(vector<int>& nums) {
        this->nums = nums;
        this->original.resize(nums.size());
        copy(nums.begin(), nums.end(), original.begin());
    }
    
    vector<int> reset() {
        copy(original.begin(), original.end(), nums.begin());
        return nums;
    }
    
    vector<int> shuffle() {
        for (int i = 0; i < nums.size(); ++i) {
            int j = i + rand() % (nums.size() - i);
            swap(nums[i], nums[j]);
        }
        return nums;
    }
private:
    vector<int> nums;
    vector<int> original;
};
```


### Rust 解法：
```
use rand::prelude::*;
struct Solution {
     nums: Vec<i32>
}


/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl Solution {

    fn new(nums: Vec<i32>) -> Self {
        Solution {
            nums
        }
    }
    
    fn reset(&self) -> Vec<i32> {
         self.nums.clone()
    }
    
    fn shuffle(&self) -> Vec<i32> {
        let mut res = self.nums.clone();
        let length = res.len();
        for i in 0..length {
            let mut rng = thread_rng();
            let x = rng.gen_range(0, length);
            res.swap(i, x);
        }
        res
    }
}
```