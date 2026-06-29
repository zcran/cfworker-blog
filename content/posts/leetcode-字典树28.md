---
title: "leetcode-字典树28"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 找出强数对的最大异或值 II


给你一个下标从 0 开始的整数数组 nums 。如果一对整数 x 和 y 满足以下条件，则称其为 强数对 ：

|x - y| <= min(x, y)

你需要从 nums 中选出两个整数，且满足：这两个整数可以形成一个强数对，并且它们的按位异或（XOR）值是在该数组所有强数对中的 最大值 。

返回数组 nums 所有可能的强数对中的 最大 异或值。

注意，你可以选择同一个整数两次来形成一个强数对。

```
impl Solution {
    pub fn maximum_strong_pair_xor(mut nums: Vec<i32>) -> i32 {
        // ========== 1. 预处理：排序并去重 ==========
        nums.sort_unstable();  // 升序排序
        nums.dedup();          // 去重（相同数字只保留一个）

        // ========== 2. 递归函数：分治求解 ==========
        // 参数：
        //   l: 左半部分数组（较小值候选）
        //   r: 右半部分数组（较大值候选）
        //   mask: 当前正在处理的位掩码（最高位）
        // 返回值：当前区间内能得到的最大异或值
        fn rec(l: &[i32], r: &[i32], mask: i32) -> i32 {
            // 递归终止条件：没有位需要处理了
            if mask == 0 { return 0; }

            // 构造选择器：mask | (mask-1) 可以得到 mask 及所有更低位都是 1
            // 例如 mask = 1000 (8)，则 selector = 1111 (15)
            // 这用于将数字按高位分组
            let selector = mask | (mask - 1);

            // 在左半部分查找第一个使 x & selector >= mask 的位置
            // 实际上是在找最高位等于 mask 的数字（因为 selector 的低位都是1）
            let i = match l.binary_search_by_key(&mask, move |&x| x & selector)
                { Ok(i) => i, Err(i) => i };

            // 在右半部分查找第一个使 x & selector >= mask 的位置
            let j = match r.binary_search_by_key(&mask, move |&x| x & selector)
                { Ok(j) => j, Err(j) => j };

            // 根据分割情况分治处理
            match (i == 0 || j == r.len(), i == l.len() || j == 0) {
                // 情况1：所有数字都在同一侧（无法形成跨组的强数对）
                (true, true) => rec(l, r, mask >> 1),

                // 情况2：左半部分有高位0，右半部分有高位1
                // 可以形成跨组异或，当前位贡献 mask
                (true, false) => rec(&l[i..], &r[..j], mask >> 1) | mask,

                // 情况3：左半部分有高位1，右半部分有高位0
                (false, true) => {
                    match (j == 0, l[i - 1] * 2 >= r[j]) {
                        // 子情况3a：右半部分全在高位1组，且满足强数对条件
                        (true, true) => rec(&l[..i], &r, mask >> 1) | mask,
                        // 子情况3b：右半部分全在高位1组，但不满足强数对
                        (true, false) => rec(&l[i..], &r, mask >> 1),
                        // 子情况3c：右半部分有分组，且满足强数对
                        (false, true) => rec(&l, &r[j..], mask >> 1) | mask,
                        // 子情况3d：右半部分有分组，但不满足强数对
                        (false, false) => rec(&l, &r[..j], mask >> 1),
                    }
                }

                // 情况4：两侧都有分组，需要比较两种可能
                _ => {
                    rec(&l[i..], &r[..j], mask >> 1).max(
                        if l[i - 1] * 2 >= r[j] {
                            rec(&l[..i], &r[j..], mask >> 1)
                        } else { 0 }
                    ) | mask
                }
            }
        }

        // ========== 3. 主循环：逐步缩小范围 ==========
        let (mut nums, mut ret) = (&nums[..], 0);

        while nums.len() > 1 {
            // 找到当前区间内最大异或值的最高位
            let diff = *nums.first().unwrap() ^ *nums.last().unwrap();
            let mask = 1 << (31 - diff.leading_zeros());  // 最高位

            // 构造选择器
            let selector = mask | (mask - 1);

            // 按当前最高位分割数组
            let i = match nums.binary_search_by_key(&mask, move |&x| x & selector)
                { Ok(i) => i, Err(i) => i };

            // 检查分割点两侧能否形成强数对
            if nums[i - 1] * 2 >= nums[i] {
                // 可以形成强数对，递归计算并更新答案
                ret = ret.max(rec(&nums[..i], &nums[i..], mask >> 1) | mask);
                break;  // 找到了最优解，退出循环
            }

            // 尝试右半部分
            if i + 1 < nums.len() {
                let nums = &nums[i..];
                let diff = *nums.first().unwrap() ^ *nums.last().unwrap();
                let mask = 1 << (31 - diff.leading_zeros());
                let selector = mask | (mask - 1);
                let i = match nums.binary_search_by_key(&mask, move |&x| x & selector)
                    { Ok(i) => i, Err(i) => i };
                ret = ret.max(rec(&nums[..i], &nums[i..], mask >> 1) | mask);
            }

            // 缩小范围到左半部分
            nums = &nums[..i];
        }

        ret
    }
}

```
