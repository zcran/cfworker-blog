---
title: "leetcode-字典树38"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 数组中两个数的最大异或值

给定一个整数数组 nums ，返回 nums[i] XOR nums[j] 的最大运算结果，其中 0 ≤ i ≤ j < n 。


```
/// 二进制Trie树（前缀树）
///
/// 用于存储整数的二进制表示（31位，从最高位到最低位）
/// 每个节点表示一个二进制位，左分支表示0，右分支表示1
struct Trie {
    /// 左子节点（二进制位为0）
    left: Option<Box<Trie>>,
    /// 右子节点（二进制位为1）
    right: Option<Box<Trie>>,
}

impl Trie {
    /// 创建一个新的Trie节点
    fn new() -> Self {
        Trie {
            left: None,
            right: None,
        }
    }

    /// 向Trie中插入一个数字
    ///
    /// 将数字的二进制表示（从第30位到第0位）插入到Trie中
    ///
    /// # 参数
    /// * `num` - 要插入的整数
    fn add_num(&mut self, num: i32) {
        let mut cur = self;

        // 从第30位遍历到第0位（共31位，适合i32范围）
        for i in (0..=30).rev() {
            // 检查当前位是0还是1
            if (num >> i) & 1 == 0 {
                // 当前位为0，走左分支
                cur = cur.left.get_or_insert(Box::new(Trie::new()));
            } else {
                // 当前位为1，走右分支
                cur = cur.right.get_or_insert(Box::new(Trie::new()));
            }
        }
    }

    /// 查找与给定数字异或结果最大的数字
    ///
    /// 贪心策略：从高位到低位，尽量选择与当前位不同的分支
    /// 因为异或运算中，不同为1，相同为0
    ///
    /// # 参数
    /// * `num` - 要匹配的数字
    ///
    /// # 返回
    /// 与num异或结果最大的值（不是异或结果，而是构造出的数字）
    fn get_max_num(&self, num: i32) -> i32 {
        let mut cur = self;
        let mut max_num = 0;

        // 从高位到低位遍历
        for i in (0..=30).rev() {
            max_num *= 2;  // 左移一位，为下一位腾出空间

            if (num >> i) & 1 == 0 {
                // 当前位为0，为了异或结果最大，应选择1（右分支）
                match (cur.left.as_ref(), cur.right.as_ref()) {
                    (_, Some(right)) => {
                        // 存在右分支（1），选择它，异或结果当前位为1
                        max_num += 1;
                        cur = right.as_ref();
                    }
                    (Some(left), None) => {
                        // 不存在右分支，只能选择左分支（0），异或结果当前位为0
                        cur = left.as_ref();
                    }
                    (None, None) => (),
                }
            } else {
                // 当前位为1，为了异或结果最大，应选择0（左分支）
                match (cur.left.as_ref(), cur.right.as_ref()) {
                    (Some(left), _) => {
                        // 存在左分支（0），选择它，异或结果当前位为1
                        max_num += 1;
                        cur = left.as_ref();
                    }
                    (None, Some(right)) => {
                        // 不存在左分支，只能选择右分支（1），异或结果当前位为0
                        cur = right.as_ref();
                    }
                    (None, None) => (),
                }
            }
        }

        max_num
    }
}

impl Solution {
    /// 找出数组中两个数的最大异或值
    ///
    /// # 算法原理
    /// 使用Trie树，对于每个数，在树中寻找与其异或结果最大的数
    /// 时间复杂度：O(n * 31) = O(n)
    /// 空间复杂度：O(n * 31)
    ///
    /// # 参数
    /// * `nums` - 整数数组
    ///
    /// # 返回
    /// 数组中两个数的最大异或值
    ///
    /// # 示例
    /// ```
    /// let nums = vec![3, 10, 5, 25, 2, 8];
    /// let result = Solution::find_maximum_xor(nums);
    /// assert_eq!(result, 28);  // 5 XOR 25 = 28
    /// ```
    pub fn find_maximum_xor(nums: Vec<i32>) -> i32 {
        let mut max_xor = 0;
        let mut trie = Trie::new();

        // 遍历数组，对于每个数，先插入前一个数，再查询当前数
        // 这样可以保证查询时树中至少有一个数
        for window in nums.windows(2) {
            trie.add_num(window[0]);  // 插入前一个数
            let xor_candidate = trie.get_max_num(window[1]);  // 查询与当前数异或最大的数
            max_xor = max_xor.max(xor_candidate);
        }

        max_xor
    }
}

/// 改进版本：处理空数组和单元素数组
impl Solution {
    /// 改进版：正确处理边界情况
    pub fn find_maximum_xor_improved(nums: Vec<i32>) -> i32 {
        if nums.len() < 2 {
            return 0;
        }

        let mut max_xor = 0;
        let mut trie = Trie::new();

        // 插入第一个数
        trie.add_num(nums[0]);

        // 从第二个数开始，每个数都与之前的所有数比较
        for &num in nums.iter().skip(1) {
            max_xor = max_xor.max(trie.get_max_num(num));
            trie.add_num(num);
        }

        max_xor
    }
}
```
