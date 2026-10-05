---
title: "leetcode-双指针27"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 环形数组是否存在循环

存在一个不含 0 的 环形 数组 nums ，每个 nums[i] 都表示位于下标 i 的角色应该向前或向后移动的下标个数：

· 如果 nums[i] 是正数，向前（下标递增方向）移动 nums[i] 步
· 如果 nums[i] 是负数，向后（下标递减方向）移动 abs(nums[i]) 步

因为数组是 环形 的，所以可以假设从最后一个元素向前移动一步会到达第一个元素，而第一个元素向后移动一步会到达最后一个元素。

数组中的 循环 由长度为 k 的下标序列 seq 标识：

· 遵循上述移动规则将导致一组重复下标序列 seq[0] -> seq[1] -> ... -> seq[k - 1] -> seq[0] -> ...
· 所有 nums[seq[j]] 应当不是 全正 就是 全负
· k > 1

如果 nums 中存在循环，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn circular_array_loop(nums: Vec<i32>) -> bool {
        let n = nums.len();
        // 0: 未访问, 1: 当前路径中, 2: 已确认无效
        let mut state = vec![0; n];
        // 记录每个节点在路径中的步数序号
        let mut step = vec![0; n];

        for i in 0..n {
            if state[i] != 0 {
                continue;
            }

            let mut cur = i;
            let mut cnt = 0; // 当前路径步数

            // 沿路径前进，标记当前路径
            while state[cur] == 0 {
                state[cur] = 1;
                step[cur] = cnt;
                cnt += 1;

                let next = ((cur as i32 + nums[cur]).rem_euclid(n as i32)) as usize;

                // 方向必须一致
                if nums[cur] * nums[next] <= 0 {
                    break;
                }
                cur = next;
            }

            // 如果进入了当前路径，说明形成了环
            if state[cur] == 1 {
                let loop_len = cnt - step[cur];
                if loop_len > 1 {
                    return true;
                }
            }

            // 将当前路径标记为无效，避免重复搜索
            cur = i;
            while state[cur] == 1 {
                state[cur] = 2;
                let next = ((cur as i32 + nums[cur]).rem_euclid(n as i32)) as usize;
                if nums[cur] * nums[next] <= 0 {
                    break;
                }
                cur = next;
            }
        }

        false
    }
}
```
