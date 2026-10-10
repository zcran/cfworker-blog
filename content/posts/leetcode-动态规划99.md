---
title: "leetcode-动态规划99"
date: 2026-10-05T20:40:00+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 移除所有载有违禁货物车厢所需的最少时间

给你一个下标从 0 开始的二进制字符串 s ，表示一个列车车厢序列。s[i] = '0' 表示第 i 节车厢 不 含违禁货物，而 s[i] = '1' 表示第 i 节车厢含违禁货物。

作为列车长，你需要清理掉所有载有违禁货物的车厢。你可以不限次数执行下述三种操作中的任意一个：

1. 从列车 左 端移除一节车厢（即移除 s[0]），用去 1 单位时间。
2. 从列车 右 端移除一节车厢（即移除 s[s.length - 1]），用去 1 单位时间。
3. 从列车车厢序列的 任意位置 移除一节车厢，用去 2 单位时间。

返回移除所有载有违禁货物车厢所需要的 最少 单位时间数。

注意，空的列车车厢序列视为没有车厢含违禁货物。


```
impl Solution {
    pub fn minimum_time(s: String) -> i32 {
        let n = s.len() as i32;
        let mut ans = i32::MAX;
        // presum: 已遍历到的 '1' 的数量
        // prebest: 维护 min(j - 2 * presum) 的最小值
        let mut presum = 0i32;
        let mut prebest = 0i32;

        for (j, b) in s.bytes().enumerate() {
            let j = j as i32;
            // 先更新 prebest，此时 presum 尚未包含当前位置的贡献
            prebest = prebest.min(j - 2 * presum);
            if b == b'1' {
                presum += 1;
            }
            ans = ans.min(prebest + 2 * presum - j);
        }

        // 最后与整体从两端删除的方案取较优
        (ans + n - 1).min(n)
    }
}
```
