---
title: "Leetcode 归并排序3"
date: 2025-05-23T21:22:00+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 计算右侧小于当前元素的个数

给你一个整数数组 nums ，按要求返回一个新数组 counts 。数组 counts 有该性质： counts[i] 的值是  nums[i] 右侧小于 nums[i] 的元素的数量。


方法一：归并排序
```

impl Solution {
    pub fn count_smaller(mut nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        let mut nums = nums.into_iter().enumerate().map(|(i, num)| (num, i)).collect::<Vec<_>>();
        let mut result = vec![0; n];

        Self::split(&mut nums, 0, n, &mut result);
        result
    }

    fn split(nums: &mut Vec<(i32, usize)>, l: usize, r: usize, result: &mut Vec<i32>) {
        if l + 1 >= r { return; }

        let mid = (l + r) >> 1;
        Self::split(nums, l, mid, result);
        Self::split(nums, mid, r, result);

        Self::merge(nums, l, mid, r, result);
    }

    fn merge(nums: &mut Vec<(i32, usize)>, l: usize, mid: usize, r: usize, result: &mut Vec<i32>) {
        let right = nums[mid..r].to_vec();
        let mut idx = r - 1;
        let mut idx1 = mid - 1;
        let mut idx2 = right.len() - 1;

        loop {
            if nums[idx1].0 > right[idx2].0 {
                result[nums[idx1].1] += idx2 as i32 + 1;
                nums[idx] = nums[idx1];
                if idx1 == l {
                    for i in 0..=idx2 {
                        nums[l + i] = right[i];
                    }
                    break; 
                } else { 
                    idx1 -= 1; 
                }
            } else {
                nums[idx] = right[idx2];
                if idx2 == 0 { break; } else { idx2 -= 1; }
            }
            idx -= 1;
        }
    }
}

```



方法二：树状数组
```
struct FenwickTree {
  tree: Vec<i32>,
}

impl FenwickTree {
  fn new(size: usize) -> Self {
    FenwickTree {
        tree: vec![0; size + 1],
    }
  }

  fn update(&mut self, pos: usize, delta: i32) {
    let mut pos = pos + 1;
    while pos < self.tree.len() {
      self.tree[pos] += delta;
      pos += pos & (!pos+1);
    }
  }

  fn query(&self, pos: usize) -> i32 {
    let mut pos = pos + 1;
    let mut value = 0;
    while pos > 0 {
      value += self.tree[pos];
      pos -= pos & (!pos+1);
    }
    value
  }
}

impl Solution {
  pub fn count_smaller(nums: Vec<i32>) -> Vec<i32> {
    let n = nums.len();
    let mx = *nums.iter().max().unwrap();
    let mn = *nums.iter().min().unwrap();
    let mut fnwick_tree = FenwickTree::new((mx - mn + 1) as usize);
    let mut ans = vec![0; n];
    for i in (0..n).rev() {
      let pos = (nums[i] - mn) as usize;
      ans[i] = fnwick_tree.query(pos-1);
      fnwick_tree.update(pos, 1);
    }
    ans
  }  
}
```