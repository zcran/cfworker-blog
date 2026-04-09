---
title: "leetcode-交互4"
date: 2026-03-31T20:54:46+08:00
tags: ["leetcode", "交互"]
draft: false
---


## 山脉数组中查找目标值

你可以将一个数组 arr 称为 山脉数组 当且仅当：

arr.length >= 3
存在一些 0 < i < arr.length - 1 的 i 使得：
arr[0] < arr[1] < ... < arr[i - 1] < arr[i]
arr[i] > arr[i + 1] > ... > arr[arr.length - 1]
给定一个山脉数组 mountainArr ，返回 最小 的 index 使得 mountainArr.get(index) == target。如果不存在这样的 index，返回 -1 。

你无法直接访问山脉数组。你只能使用 MountainArray 接口来访问数组：

MountainArray.get(k) 返回数组中下标为 k 的元素（从 0 开始）。
MountainArray.length() 返回数组的长度。
调用 MountainArray.get 超过 100 次的提交会被判定为错误答案。此外，任何试图绕过在线评测的解决方案都将导致取消资格。


```
impl Solution {
    pub fn find_in_mountain_array(target: i32, mountainArr: &MountainArray) -> i32 {

        let (mut l,mut len)=(0,mountainArr.length());
        let mut r = len-1;


        while l<r {  //找山顶
            let m=(l+r)/2;
            if mountainArr.get(m) <mountainArr.get(m+1){
                l=m+1;
            }else{
                r=m;
            }
        } let max =l;

        r = max; l = 0;   // 左边
        while l<=r{
            let m = (l+r)/2;
            let tmp = mountainArr.get(m);
            if tmp == target { return m; }
            else if tmp<target{ l = m+1; }
            else { r = m-1; }
        }


        l = max;r = len-1; //右边
         while l<=r{
            let m = (l+r)/2;
            let tmp = mountainArr.get(m);
            if tmp == target { return m; }
            else if tmp<target{ r = m-1; }
            else { l = m+1; }
        }

        -1
    }
}
```
