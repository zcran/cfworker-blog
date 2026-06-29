---
title: "leetcode-数论70"
date: 2026-06-20T11:09:58+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 切分数组

给定一个整数数组 nums ，小李想将 nums 切割成若干个非空子数组，使得每个子数组最左边的数和最右边的数的最大公约数大于 1 。为了减少他的工作量，请求出最少可以切成多少个子数组。


```
impl Solution {
    /// 将数组切割成若干非空子数组，使每个子数组首尾元素的 GCD > 1
    /// 返回最少切割数
    ///
    /// 算法核心：用最小质因子进行状态压缩，贪心+DP
    /// 时间复杂度 O(n log M)，空间复杂度 O(M)
    pub fn split_array(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n == 0 { return 0; }
        if n == 1 { return 1; }

        // 找到数组最大值
        let max_val = *nums.iter().max().unwrap() as usize;
        let limit = max_val.max(2);

        // 线性筛：min_prime[i] = i 的最小质因子
        let mut min_prime = vec![0; limit + 1];
        let mut primes = Vec::with_capacity(limit / 10);
        for i in 2..=limit {
            if min_prime[i] == 0 {
                min_prime[i] = i;
                primes.push(i);
            }
            for &p in &primes {
                let t = i * p;
                if t > limit { break; }
                min_prime[t] = p;
                if i % p == 0 { break; }
            }
        }

        // g[p] = 以包含质因子 p 的元素结尾的子数组的最小起始位置
        // 初始化为 n（表示无穷大）
        let mut g = vec![n; limit + 1];

        // 处理第一个元素：以 nums[0] 的质因子结尾的子数组起始位置为 0
        let mut x = nums[0] as usize;
        while x > 1 {
            let p = min_prime[x];
            g[p] = 0;
            while x % p == 0 { x /= p; } // 去重
        }

        let mut ans = 1; // 当前最少切割数

        for i in 0..n {
            // 计算以 nums[i] 结尾的最少切割数
            // 初始值：从开头到 i，每个元素单独成一段
            let mut cur_ans = i + 1;

            // 枚举 nums[i] 的所有质因子，取最小的 g[p] + 1
            let mut x = nums[i] as usize;
            while x > 1 {
                let p = min_prime[x];
                cur_ans = cur_ans.min(g[p] + 1);
                while x % p == 0 { x /= p; }
            }

            // 更新最终答案
            ans = cur_ans;

            // 如果已经处理到最后一个元素，退出
            if i == n - 1 { break; }

            // 用当前的 ans 更新下一个元素的质因子 g 值
            // 即：以 nums[i+1] 的质因子结尾的子数组起始位置可以更小
            let mut x = nums[i + 1] as usize;
            while x > 1 {
                let p = min_prime[x];
                g[p] = g[p].min(ans);
                while x % p == 0 { x /= p; }
            }
        }

        ans as i32
    }
}
```
