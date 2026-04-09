---
title: "leetcode-单调队列21"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 最大子数组异或值

给你一个非负整数数组 nums 和一个整数 k。

你需要选择 nums 的一个 子数组，使得该子数组中元素的 最大值 与 最小值 之间的差值不超过 k。这个子数组的 值 定义为子数组中所有元素按位异或（XOR）的结果。

返回一个整数，表示所选子数组可能获得的 最大值 。

子数组 是数组中任意连续、非空 的元素序列。



```
impl Solution {
    pub fn max_xor(nums: Vec<i32>, k: i32) -> i32 {
        use std::collections::VecDeque;
        let n=nums.len();
        let (mut lefts,mut mn,mut mx,mut l)=(vec![0;n],VecDeque::new(),VecDeque::new(),0);
        for (r,&x) in nums.iter().enumerate(){        // 遍历数组，r是右端点索引，x是当前值。
            while mn.back().is_some_and(|&i| x<=nums[i] ){
                mn.pop_back();      // 从队尾移除所有值 >= x 的索引（保持队列递增）
            }
            mn.push_back(r);        // 将当前索引入队
            while mx.back().is_some_and(|&i| x>=nums[i] ){
                mx.pop_back();        // 从队尾移除所有值 <= x 的索引（保持队列递减）
            }
            mx.push_back(r);      // 将当前索引入队
            while nums[*mx.front().unwrap()]-nums[*mn.front().unwrap()]>k{
                l+=1;     // 左边界右移
                if *mn.front().unwrap()<l{
                    mn.pop_front();     // 移除移出窗口的最小值
                }
                if *mx.front().unwrap()<l{
                    mx.pop_front();     // 移除移出窗口的最大值
                }
            }       // 当窗口内最大值-最小值 > k时，不断右移左边界。同时从队列中移除移出窗口的索引
            lefts[r]=l;     // 记录以r为右端点时，合法子数组的最左边界。
        }


        let w=32-(*nums.iter().max().unwrap()).leading_zeros();   // 计算数组中最大值的二进制位数（如最大值是15，则w=4）。
        let pre:Vec<_>=[0].iter().chain(&nums).scan(0,|s,x| {*s^=x;Some(*s)}).collect();      // 计算前缀异或


        // 按位构造最大异或值（贪心算法）
        let mut ans=0;
        for i in (0..w).rev(){
            let mut last=vec![-1;1<<(w-i)];
            last[0]=0;      // last数组用于记录前缀异或值最后出现的位置。大小 = 2^(剩余位数)，索引是前缀异或值（右对齐后的值）

            ans<<=1;   // 当前答案左移一位，为下一位腾出空间
            let new_ans=ans|1;   // 尝试将当前位设为1
            for r in 0..n{
                let s=pre[r+1]>>i;   // 前缀异或值右移i位（保留高位）
                if last[(new_ans^s) as usize]>=lefts[r] as i32{
                    ans=new_ans;   // 可以做到，保持这一位为1
                    break
                }
                last[s as usize]=r as i32+1;   // 记录s最后出现的位置
            }
        }
        ans
    }
}
```


lefts: 存储每个位置作为右端点时，合法子数组的最左边界
mn: 单调递增队列，维护窗口最小值索引
mx: 单调递减队列，维护窗口最大值索引
l: 当前窗口的左边界指针
