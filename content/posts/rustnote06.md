---
title: "Rust Note 06"
date: 2023-05-19T21:35:39+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
## 两个数组的交集

给你两个整数数组 nums1 和 nums2 ，请你以数组形式返回两数组的交集。返回结果中每个元素出现的次数，应与元素在两个数组中都出现的次数一致（如果出现次数不一致，则考虑取较小值）。可以不考虑输出结果的顺序。


## C解法 ： 
```
int* intersect(int* nums1, int nums1Size, int* nums2, int nums2Size, int* returnSize)
{
    int* rearr = (int*)malloc(sizeof(int)*(nums1Size>nums2Size?nums2Size:nums1Size));
    int a[1001] = {0};
    int b[1001] = {0};
    for(int i = 0; i<nums1Size; i++)
    {
        a[nums1[i]]++;
    }
    for(int i = 0; i<nums2Size; i++)
    {
        b[nums2[i]]++;
    }

    *returnSize = 0;
    for(int i = 0; i<1001; i++)
    {
        if(a[i]>0&&b[i]>0)
        {
            int n = (a[i]>b[i])?b[i]:a[i];
            for(int j = 0; j<n ;j++)
            {
                rearr[*returnSize] = i;
                (*returnSize)++;
            }
        }
    }
    return rearr;
}
```

## Rust 解法 ：
```
use std::collections::HashMap;
impl Solution {
    pub fn intersect(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
        let mut ans = vec![];
        let mut map = HashMap::new();
        for n in &nums1 {
            *map.entry(n).or_insert(0) += 1;
        }
        for n in nums2 {
            if let Some(cnt) = map.get_mut(&n) {
                if *cnt > 0 {
                    *cnt -= 1;
                    ans.push(n);
                }
            }
        }
        ans
    }
}
```