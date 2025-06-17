---
title: "Leetcode 归并排序5"
date: 2025-05-23T21:22:00+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 翻转对

给定一个数组 nums ，如果 i < j 且 nums[i] > 2*nums[j] 我们就将 (i, j) 称作一个重要翻转对。

你需要返回给定数组中的重要翻转对的数量。

示例 1:
输入: [1,3,2,3,1]
输出: 2

示例 2:
输入: [2,4,3,5,1]
输出: 3

注意:
给定数组的长度不会超过50000。
输入数组中的所有数字都在32位整数的表示范围内。

```
impl Solution {
    pub fn reverse_pairs(mut nums: Vec<i32>) -> i32 {
        if nums.is_empty()
        {
            return 0;
        }
        let len  = nums.len();
        let mut temp = vec![0;len];
        return merge_sort(&mut nums,&mut temp,0,len-1) as i32;
    }
}

fn merge_sort(nums:&mut Vec<i32>,temp:&mut Vec<i32>,left:usize,right:usize) -> usize{
    if left >= right {
        return 0;
    }
    let middle = left + (right - left) /2 ;
    let mut count = merge_sort(nums,temp,left,middle) + merge_sort(nums,temp,middle+1,right);
    let mut i = left;
    let mut j = middle+1;
    while(i<=middle&&j<=right)
    {
        if(nums[i] as i64 > 2*nums[j] as i64)
        {
            count += middle - i + 1;
            j+=1;
        }else{
            i+=1;
        }
    }
    merge(nums,temp,left,middle,right);
    count

}

fn merge(nums:&mut Vec<i32>,temp:&mut Vec<i32>,left:usize,middle:usize,right:usize){
    let mut index  = 0;
    let mut i = left;
    let mut j = middle+1;
    while(i<=middle&&j<=right)
    {
        if(nums[i]<nums[j])
        {
            temp[index] = nums[i];
            index+=1;
            i+=1;
        }else{
            temp[index] = nums[j];
            index+=1;
            j+=1;
        }
    }
    while i<=middle{
        temp[index] = nums[i];
        index+=1;
        i+=1;
    }
    while(j<=right)
    {
        temp[index] = nums[j];
        index+=1;
        j+=1;
    }
    for i in left..=right{
        nums[i] = temp[i-left];
    }
}

```