---
title: "leetcode-双指针77"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 邻位交换的最小次数

给你一个表示大整数的字符串 num ，和一个整数 k 。

如果某个整数是 num 中各位数字的一个 排列 且它的 值大于 num ，则称这个整数为 妙数 。可能存在很多妙数，但是只需要关注 值最小 的那些。

例如，num = "5489355142" ：
    第 1 个最小妙数是 "5489355214"
    第 2 个最小妙数是 "5489355241"
    第 3 个最小妙数是 "5489355412"
    第 4 个最小妙数是 "5489355421"

返回要得到第 k 个 最小妙数 需要对 num 执行的 相邻位数字交换的最小次数 。

测试用例是按存在第 k 个最小妙数而生成的。


```
impl Solution {
    pub fn get_min_swaps(num: String, k: i32) -> i32 {
        let digits: Vec<u8> = num.bytes().map(|b| b - b'0').collect();
        let mut target = digits.clone();

        // 生成第 k 个下一个排列
        for _ in 0..k {
            next_permutation(&mut target);
        }

        // 计算将原始数字转换为目标排列所需的最小相邻交换次数
        let mut swaps = 0;
        let mut current = digits;

        for i in 0..current.len() {
            if current[i] == target[i] {
                continue;
            }

            // 在 current 中找 target[i] 的位置
            let j = current[i..].iter().position(|&x| x == target[i]).unwrap() + i;

            // 将 target[i] 通过相邻交换移动到 i 位置
            for pos in (i..j).rev() {
                current.swap(pos, pos + 1);
                swaps += 1;
            }
        }

        swaps
    }
}

/// 计算下一个字典序排列（原地修改）
fn next_permutation(nums: &mut [u8]) {
    let n = nums.len();
    if n <= 1 {
        return;
    }

    // 从右向左找第一个非递增位置
    let mut i = n - 2;
    while i < n && nums[i] >= nums[i + 1] {
        if i == 0 { break; }
        i -= 1;
    }

    // 如果找到，从右向左找第一个比 nums[i] 大的数并交换
    if nums[i] < nums[i + 1] {
        let mut j = n - 1;
        while nums[j] <= nums[i] {
            j -= 1;
        }
        nums.swap(i, j);
        i += 1;
    } else {
        i = 0;
    }

    // 反转后缀
    nums[i..].reverse();
}
```
