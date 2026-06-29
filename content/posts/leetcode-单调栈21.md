---
title: "leetcode-单调栈21"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 表现良好的最长时间段


给你一份工作时间表 hours，上面记录着某一位员工每天的工作小时数。

我们认为当员工一天中的工作小时数大于 8 小时的时候，那么这一天就是「劳累的一天」。

所谓「表现良好的时间段」，意味在这段时间内，「劳累的天数」是严格 大于「不劳累的天数」。

请你返回「表现良好时间段」的最大长度。


方法1，前缀和+数组
```前缀和+数组
impl Solution {
    /// 寻找表现良好的最长时间段（疲劳天数 > 8 的天数 > 一半）
    ///
    /// # 思路
    /// 1. 将 >8 转为 +1，<=8 转为 -1
    /// 2. 问题转化为：找最长子数组，其和 > 0
    /// 3. 维护前缀和 pre，若 pre > 0 则 [0..i] 即为答案候选
    /// 4. 否则找最早的 pre-1 出现位置，构成 (pre-1, pre] 的和为 1 的子数组
    ///
    /// # 优化点
    /// - 使用数组替代 HashMap（因为前缀和范围在 [-n, n]）
    /// - 预分配容量，避免动态扩容
    /// - 一次遍历，O(n) 时间 O(n) 空间
    pub fn longest_wpi(hours: Vec<i32>) -> i32 {
        let n = hours.len();
        // 前缀和范围：[-n, n]，偏移 n 映射到 [0, 2n]
        let offset = n as i32;
        // 记录每个前缀和第一次出现的位置，初始化为 n+1 表示未出现
        let mut first_occurrence = vec![n + 1; 2 * n + 1];
        let mut ans = 0;
        let mut pre = 0;

        // 前缀和 0 出现在位置 0（虚拟位置）
        first_occurrence[offset as usize] = 0;

        for (i, &x) in hours.iter().enumerate() {
            // 转换：>8 为 +1，否则为 -1
            pre += if x > 8 { 1 } else { -1 };
            let idx = (pre + offset) as usize;

            if pre > 0 {
                // 前缀和 > 0，说明 [0..i] 全部满足条件
                ans = i + 1;
            } else if pre - 1 + offset >= 0 {
                // 查找 pre-1 第一次出现的位置
                let prev_idx = first_occurrence[(pre - 1 + offset) as usize];
                if prev_idx != n + 1 {
                    // 子数组 (prev_idx..=i] 的和为 1
                    ans = ans.max(i + 1 - prev_idx);
                }
            }

            // 记录当前前缀和第一次出现的位置
            if first_occurrence[idx] == n + 1 {
                first_occurrence[idx] = i + 1;
            }
        }

        ans as i32
    }
}
```

方法2，HashMap
```HashMap
impl Solution {
    pub fn longest_wpi(hours: Vec<i32>) -> i32 {
        let mut map = HashMap::with_capacity(hours.len());
        let mut ans = 0;
        let mut pre = 0;
        map.insert(0, 0);

        for (i, &h) in hours.iter().enumerate() {
            pre += if h > 8 { 1 } else { -1 };

            if pre > 0 {
                ans = i + 1;
            } else {
                if let Some(&pos) = map.get(&(pre - 1)) {
                    ans = ans.max(i + 1 - pos);
                }
            }

            map.entry(pre).or_insert(i + 1);
        }

        ans as i32
    }
}
```
