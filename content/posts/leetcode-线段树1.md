---
title: "leetcode-线段树1"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 找到最接近目标值的函数值

```
func(arr, l, r) {
  if (r < l) {
    return -1000000000
  }
  ans = arr[i]
  for (i = l + i; i <= r; i++>) {
    ans = ans & arr[i]
  }
  return ans
}
```
Winston 构造了一个如上所示的函数 func 。他有一个整数数组 arr 和一个整数 target ，他想找到让 |func(arr, l, r) - target| 最小的 l 和 r 。

请你返回 |func(arr, l, r) - target| 的最小值。

请注意， func 的输入参数 l 和 r 需要满足 0 <= l, r < arr.length 。



```
impl Solution {
    pub fn closest_to_target(arr: Vec<i32>, target: i32) -> i32 {
        let n = arr.len();
        let mut ans = i32::MAX;
        // 存储以当前元素结尾的所有子数组的按位与结果
        // 使用 Vec 而不是 HashSet 来保持顺序，减少内存分配
        let mut cur = Vec::new();

        for &num in &arr {
            let mut new_cur = Vec::with_capacity(cur.len() + 1);
            // 当前元素单独作为一个子数组
            new_cur.push(num);
            // 将当前元素与之前的所有结果进行按位与
            for &val in &cur {
                let and_val = val & num;
                // 去重：只添加与前一个不同的值
                if let Some(&last) = new_cur.last() {
                    if last != and_val {
                        new_cur.push(and_val);
                    }
                } else {
                    new_cur.push(and_val);
                }
            }
            cur = new_cur;
            // 更新答案
            for &val in &cur {
                ans = ans.min((val - target).abs());
                if ans == 0 {
                    return 0;
                }
            }
        }
        ans
    }
}
```
