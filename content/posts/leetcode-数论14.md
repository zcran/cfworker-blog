---
title: "leetcode-数论14"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 数组的最大公因数排序


给你一个整数数组 nums ，你可以在 nums 上执行下述操作 任意次 ：

如果 gcd(nums[i], nums[j]) > 1 ，交换 nums[i] 和 nums[j] 的位置。其中 gcd(nums[i], nums[j]) 是 nums[i] 和 nums[j] 的最大公因数。

如果能使用上述交换方式将 nums 按 非递减顺序 排列，返回 true ；否则，返回 false 。



```
impl Solution {
    /// 判断是否能通过交换具有非1公约数的元素对将数组排序为非递减顺序
    ///
    /// # 思路
    /// 使用并查集将所有通过质因子相连的元素归为同一组，同一组内的元素可以任意交换。
    /// 排序后检查每个位置的元素是否与原始元素在同一组中。
    pub fn gcd_sort(nums: Vec<i32>) -> bool {
        if nums.len() <= 1 {
            return true;
        }

        let max_val = *nums.iter().max().unwrap() as usize;
        let mut dsu = DSU::new(max_val + 1);
        let smallest_prime_factor = smallest_prime_factor_sieve(max_val);

        // 将每个数与它的质因子合并
        for &num in &nums {
            let mut x = num as usize;
            while x > 1 {
                let p = smallest_prime_factor[x];
                dsu.union(num as usize, p);
                while x % p == 0 {
                    x /= p;
                }
            }
        }

        // 排序并检查
        let mut sorted = nums.clone();
        sorted.sort_unstable();
        for i in 0..nums.len() {
            if nums[i] != sorted[i] && !dsu.connected(nums[i] as usize, sorted[i] as usize) {
                return false;
            }
        }
        true
    }
}

/// 并查集（Disjoint Set Union）
struct DSU {
    parent: Vec<usize>,
    rank: Vec<u8>,
}

impl DSU {
    fn new(n: usize) -> Self {
        Self {
            parent: (0..n).collect(),
            rank: vec![0; n],
        }
    }

    /// 查找根节点（路径压缩）
    fn find(&mut self, x: usize) -> usize {
        if self.parent[x] != x {
            self.parent[x] = self.find(self.parent[x]);
        }
        self.parent[x]
    }

    /// 合并两个集合（按秩合并）
    fn union(&mut self, a: usize, b: usize) {
        let (ra, rb) = (self.find(a), self.find(b));
        if ra == rb {
            return;
        }
        match self.rank[ra].cmp(&self.rank[rb]) {
            std::cmp::Ordering::Less => self.parent[ra] = rb,
            std::cmp::Ordering::Greater => self.parent[rb] = ra,
            std::cmp::Ordering::Equal => {
                self.parent[rb] = ra;
                self.rank[ra] += 1;
            }
        }
    }

    /// 检查两个节点是否连通
    fn connected(&mut self, a: usize, b: usize) -> bool {
        self.find(a) == self.find(b)
    }
}

/// 使用线性筛法计算每个数的最小质因子
fn smallest_prime_factor_sieve(n: usize) -> Vec<usize> {
    let mut spf = vec![0; n + 1];
    let mut primes = Vec::new();

    for i in 2..=n {
        if spf[i] == 0 {
            spf[i] = i;
            primes.push(i);
        }
        for &p in &primes {
            if p > spf[i] || i * p > n {
                break;
            }
            spf[i * p] = p;
        }
    }
    spf
}
```
