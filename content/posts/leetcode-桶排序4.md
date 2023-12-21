---
title: "Leetcode 桶排序4"
date: 2023-12-19T15:46:22+08:00
tags: ["leetcode", "桶排序"]
draft: false
---

## 前 K 个高频元素

给你一个整数数组 nums 和一个整数 k ，请你返回其中出现频率前 k 高的元素。你可以按 任意顺序 返回答案。

```
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int,int> mp;
    vector<int> res;
    //统计最大的频次，决定开多大的空间
    int max_cnt=0;
    for(auto num:nums) {
        mp[num]++;
        max_cnt=max(max_cnt,mp[num]);
    }
    vector<vector<int>> bucket(max_cnt+1);
    //计数，桶号为频次，元素为数字
    for(auto iter:mp){
        bucket[iter.second].push_back(iter.first);
    }
    for(int i=bucket.size()-1;i>=0 && k;i--){
        for(auto num:bucket[i]){
            if(k--) res.push_back(num);
        else break;
        }
    }
    return res;
    }
};
```

```
use std::{
    cmp::Reverse,
    collections::{BinaryHeap, HashMap},
};

impl Solution {
    pub fn top_k_frequent(nums: Vec<i32>, k: i32) -> Vec<i32> {
        let mut map = HashMap::new();
        nums.into_iter().for_each(|x| {
            map.entry(x).and_modify(|x| *x += 1).or_insert(1);
        });
        let mut heap = BinaryHeap::with_capacity(k as usize + 1);
        map.iter().take(k as usize).for_each(|x| {
            heap.push((Reverse(x.1), x.0));
        });
        map.iter().skip(k as usize).for_each(|x| {
            heap.push((Reverse(x.1), x.0));
            heap.pop();
        });
        heap.into_sorted_vec().into_iter().map(|x| *x.1).collect()
    }
}
```