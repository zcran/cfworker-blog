---
title: "Rust Note 32"
date: 2023-06-10T19:51:15+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 合并两个有序数组

给你两个按 非递减顺序 排列的整数数组 nums1 和 nums2，另有两个整数 m 和 n ，分别表示 nums1 和 nums2 中的元素数目。

请你 合并 nums2 到 nums1 中，使合并后的数组同样按 非递减顺序 排列。

注意：最终，合并后数组不应由函数返回，而是存储在数组 nums1 中。为了应对这种情况，nums1 的初始长度为 m + n，其中前 m 个元素表示应合并的元素，后 n 个元素为 0 ，应忽略。nums2 的长度为 n 。

### C 解法：
```
  // 逆向双指针
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int p1 = m - 1, p2 = n - 1;
        int tail = m + n - 1;
        int cur;
        while (p1 >= 0 || p2 >= 0) {
            if (p1 == -1) {
                cur = nums2[p2--];
            } else if (p2 == -1) {
                cur = nums1[p1--];
            } else if (nums1[p1] > nums2[p2]) {
                cur = nums1[p1--];
            } else {
                cur = nums2[p2--];
            }
            nums1[tail--] = cur;
        }
    }
};
```


### Rust 解法：
```
use std::cmp::Ordering::{Less, Equal, Greater};
impl Solution {
    pub fn merge(nums1: &mut Vec<i32>, m: i32, nums2: &mut Vec<i32>, n: i32) {
        let mut cur1 = m-1;
        let mut cur2 = (m+n-1) as usize;
        let mut cur3 = n-1;
        while cur1>=0 || cur3>=0{
            // println!("{0}, {1}", cur1, cur3);
            if cur1 >=0 && cur3 >= 0 {
                match nums1[cur1 as usize].cmp(&nums2[cur3 as usize]) {
                    Equal | Less => {
                        nums1[cur2] = nums2[cur3 as usize];
                        cur2 -= 1;
                        cur3 -= 1;
                    },
                    Greater => {
                        if cur1 != cur2 as i32 {
                            nums1[cur2] = nums1[cur1 as usize];
                        }
                        cur2 -= 1;
                        cur1 -= 1;
                    }
                }
            } else if cur3 >= 0{
                nums1[cur2] = nums2[cur3 as usize];
                cur2 -= 1;
                cur3 -= 1;
            } else {
                break;
            }
        }
    }
}
```