---
title: "leetcode-分治41"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 库存管理 III

仓库管理员以数组 stock 形式记录商品库存表，其中 stock[i] 表示对应商品库存余量。请返回库存余量最少的 cnt 个商品余量，返回 顺序不限。

双百
```
impl Solution {
    pub fn inventory_management(mut arr: Vec<i32>, k: i32) -> Vec<i32> {
        let len = arr.len();
        let k = k as usize;
        let mut ans = vec![0; k];
        if k == 0 {
            return ans;
        }
        if k == len {
            return arr;
        }
        let mut left = 0;
        let mut right = len - 1;
        loop {
            let mut i = left;
            let mut j = right;
            while i < j {
                while i < j && arr[j] >= arr[left] {
                    j -= 1;
                }
                while i < j && arr[i] <= arr[left] {
                    i += 1;
                }
                arr.swap(i, j);
            }
            arr.swap(left, i);
            if i == k {
                break;
            } else if i < k {
                left = i + 1;
            } else {
                right = i - 1;
            }
        }
        for i in 0..k {
            ans[i] = arr[i];
        }
        return ans;
    }
}
```



```
impl Solution {
    pub fn inventory_management(mut stock: Vec<i32>, cnt: i32) -> Vec<i32> {
        stock.sort();
        stock[..cnt as usize].to_vec()
    }
}
```
