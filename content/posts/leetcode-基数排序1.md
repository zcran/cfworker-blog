---
title: "Leetcode 基数排序1"
date: 2023-12-19T15:14:04+08:00
tags: ["leetcode", "基数排序"]
draft: false
---

## 裁剪数字后查询第 K 小的数字

给你一个下标从 0 开始的字符串数组 nums ，其中每个字符串 长度相等 且只包含数字。

再给你一个下标从 0 开始的二维整数数组 queries ，其中 queries[i] = [k^i, trim^i] 。对于每个 queries[i] ，你需要：

将 nums 中每个数字 裁剪 到剩下 最右边 trim^i 个数位。
在裁剪过后的数字中，找到 nums 中第 k^i 小数字对应的 下标 。如果两个裁剪后数字一样大，那么下标 更小 的数字视为更小的数字。
将 nums 中每个数字恢复到原本字符串。
请你返回一个长度与 queries 相等的数组 answer，其中 answer[i]是第 i 次查询的结果。

提示：

裁剪到剩下最右边 x 个数位的意思是不断删除最左边的数位，直到剩下 x 个数位。
nums 中的字符串可能会有前导 0 。

```
impl Solution {
    pub fn smallest_trimmed_numbers(nums: Vec<String>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let l = nums.len();
        let slen = nums[0].len();
        let mut res = Vec::new();

        for q in queries {
            let k = q[0];
            let tri = q[1] as usize;
            let mut vt = Vec::new();

            for i in 0..l {
                vt.push(i);
            }
            vt.sort_by(
                |a,b| 
                { 
                    for i in slen-tri..slen {
                        let ta = nums[*a].as_bytes()[i];
                        let tb = nums[*b].as_bytes()[i];
                        if (ta < tb){
                            return std::cmp::Ordering::Less;
                        } else if (ta > tb) {
                            return std::cmp::Ordering::Greater;
                        } else {
                            continue;
                        }
                    }
                    return std::cmp::Ordering::Equal;
                }
            );
            res.push(vt[k as usize - 1] as i32);
        }
        return res;
    }
}
```