---
title: "leetcode-双指针99"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 合并两个二维数组 - 求和法

给你两个 二维 整数数组 nums1 和 nums2.

· nums1[i] = [idi, vali] 表示编号为 idi 的数字对应的值等于 vali 。
· nums2[i] = [idi, vali] 表示编号为 idi 的数字对应的值等于 vali 。

每个数组都包含 互不相同 的 id ，并按 id 以 递增 顺序排列。

请你将两个数组合并为一个按 id 以递增顺序排列的数组，并符合下述条件：

· 只有在两个数组中至少出现过一次的 id 才能包含在结果数组内。
· 每个 id 在结果数组中 只能出现一次 ，并且其对应的值等于两个数组中该 id 所对应的值求和。如果某个数组中不存在该 id ，则假定其对应的值等于 0 。

返回结果数组。返回的数组需要按 id 以递增顺序排列。


```
impl Solution {
    pub fn merge_arrays(nums1: Vec<Vec<i32>>, nums2: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let (n1, n2) = (nums1.len(), nums2.len());
        let (mut i, mut j) = (0, 0);
        let mut res = Vec::with_capacity(n1 + n2);

        // 双指针按 id 升序合并
        while i < n1 && j < n2 {
            let (id1, v1) = (nums1[i][0], nums1[i][1]);
            let (id2, v2) = (nums2[j][0], nums2[j][1]);

            match id1.cmp(&id2) {
                std::cmp::Ordering::Equal => {
                    res.push(vec![id1, v1 + v2]);
                    i += 1;
                    j += 1;
                }
                std::cmp::Ordering::Less => {
                    res.push(vec![id1, v1]);
                    i += 1;
                }
                std::cmp::Ordering::Greater => {
                    res.push(vec![id2, v2]);
                    j += 1;
                }
            }
        }

        // 追加剩余元素
        res.extend(nums1.into_iter().skip(i));
        res.extend(nums2.into_iter().skip(j));

        res
    }
}
```
