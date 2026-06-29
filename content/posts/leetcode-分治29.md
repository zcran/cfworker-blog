---
title: "leetcode-分治29"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 区间乘法查询后的异或 II


给你一个长度为 n 的整数数组 nums 和一个大小为 q 的二维整数数组 queries，其中 queries[i] = [li, ri, ki, vi]。

对于每个查询，需要按以下步骤依次执行操作：

· 设定 idx = li。
· 当 idx <= ri 时：
    · 更新：nums[idx] = (nums[idx] * vi) % (10^9 + 7)。
    · 将 idx += ki。

在处理完所有查询后，返回数组 nums 中所有元素的 按位异或 结果。


```
impl Solution {
    /// 执行区间乘法后计算异或结果（分块优化版本）
    ///
    /// # 参数
    /// - `nums`: 原始整数数组
    /// - `queries`: 查询数组，每个查询为 [l, r, k, v]
    ///   - 对下标满足 l ≤ i ≤ r 且 (i - l) % k == 0 的元素乘以 v
    ///
    /// # 返回
    /// 所有操作完成后，数组所有元素的异或值
    ///
    /// # 算法原理
    /// 使用分块思想优化：
    /// - 小步长 (k < sqrt(n))：使用差分数组批量处理
    /// - 大步长 (k >= sqrt(n))：直接遍历更新（步长大，遍历次数少）
    pub fn xor_after_queries(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> i32 {
        const MOD: i64 = 1_000_000_007;  // 乘法取模的模数
        let mut nums: Vec<i64> = nums.into_iter().map(|x| x as i64).collect();
        let n = nums.len();
        let block_size = (n as f64).sqrt() as usize;  // 分块阈值

        // 按步长 k 分组存储查询（只存储小步长的查询）
        // groups[k] 存储所有步长为 k 的查询 (l, r, v)
        let mut groups: Vec<Vec<(usize, usize, i64)>> = vec![vec![]; block_size];

        // 分类处理查询：小步长加入分组，大步长直接执行
        for query in queries {
            let left = query[0] as usize;
            let right = query[1] as usize;
            let step = query[2] as usize;
            let multiplier = query[3] as i64;

            if step < block_size {
                // 小步长：延迟处理，通过差分数组批量更新
                groups[step].push((left, right, multiplier));
            } else {
                // 大步长：直接遍历更新（因为步长大，遍历次数少）
                let mut index = left;
                while index <= right {
                    nums[index] = nums[index] * multiplier % MOD;
                    index += step;
                }
            }
        }

        // 处理小步长查询（使用差分数组批量应用乘法）
        let mut diff = vec![1; n + block_size];  // 差分数组，长度为 n+块大小（防止越界）

        for step in 1..block_size {
            if groups[step].is_empty() {
                continue;  // 没有该步长的查询，跳过
            }

            // 重置差分数组为初始值 1（乘法的单位元）
            diff.fill(1);

            // 构建差分数组：每个查询 [left, right] 对应区间乘法
            for &(left, right, multiplier) in &groups[step] {
                // 区间开始位置：乘以 multiplier
                diff[left] = diff[left] * multiplier % MOD;

                // 区间结束位置的下一位置：乘以 multiplier 的逆元（实现区间撤销）
                // 计算区间覆盖的元素个数：((right - left) / step + 1)
                let count = (right - left) / step + 1;
                let end_index = left + count * step;  // 结束位置的下一个步长位置
                diff[end_index] = diff[end_index] * Self::mod_inverse(multiplier, MOD) % MOD;
            }

            // 前缀累积：将差分数组转换为每个位置的实际乘数
            for i in step..n {
                diff[i] = diff[i] * diff[i - step] % MOD;
            }

            // 应用乘数到原数组
            for i in 0..n {
                nums[i] = nums[i] * diff[i] % MOD;
            }
        }

        // 计算最终数组的异或值
        nums.into_iter().fold(0, |result, value| result ^ value as i32)
    }

    /// 快速幂算法：计算 x^y mod m
    ///
    /// # 参数
    /// - `base`: 底数
    /// - `exponent`: 指数
    /// - `modulus`: 模数
    ///
    /// # 返回
    /// 幂运算结果模 modulus
    fn mod_pow(mut base: i64, mut exponent: i64, modulus: i64) -> i64 {
        let mut result = 1;
        while exponent > 0 {
            if exponent & 1 == 1 {  // 当前二进制位为 1
                result = result * base % modulus;
            }
            base = base * base % modulus;  // 底数平方
            exponent >>= 1;  // 右移一位
        }
        result
    }

    /// 计算模逆元（使用费马小定理）
    ///
    /// # 参数
    /// - `x`: 需要求逆元的数（必须与模数互质）
    /// - `modulus`: 模数（质数）
    ///
    /// # 返回
    /// x 在模 modulus 下的逆元
    fn mod_inverse(x: i64, modulus: i64) -> i64 {
        // 根据费马小定理：x^(p-2) ≡ x^(-1) (mod p)
        Self::mod_pow(x, modulus - 2, modulus)
    }
}
```
