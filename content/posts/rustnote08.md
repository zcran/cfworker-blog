---
title: "Rust Note 08"
date: 2023-05-19T21:35:46+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
## 移动零
给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

请注意 ，必须在不复制数组的情况下原地对数组进行操作。

### C 解法：
```
class Solution {
    public void moveZeroes(int[] nums) {
        int indexNow = 0;
        int indexNum = 0;
        int m = nums.length;

        while(indexNum<m){
            if(nums[indexNum] != 0) {
                nums[indexNow++] = nums[indexNum];
            }
            ++indexNum;
        }

        for(int i = indexNow; i < m; i++){
            nums[i] = 0;
        }
    }
}
```


### Rust 解法：
```
impl Solution {
    pub fn move_zeroes(nums: &mut Vec<i32>) {
        let len = nums.len();
        nums.retain(|x| *x != 0);
        nums.resize(len, 0);
    }
}
```