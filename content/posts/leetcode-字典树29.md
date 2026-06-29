---
title: "leetcode-字典树29"
date: 2026-06-02T10:23:08+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 最长公共前缀的长度

给你两个 正整数 数组 arr1 和 arr2 。

正整数的 前缀 是其 最左边 的一位或多位数字组成的整数。例如，123 是整数 12345 的前缀，而 234 不是 。

设若整数 c 是整数 a 和 b 的 公共前缀 ，那么 c 需要同时是 a 和 b 的前缀。例如，5655359 和 56554 有公共前缀 565 和 5655，而 1223 和 43456 没有 公共前缀。

你需要找出属于 arr1 的整数 x 和属于 arr2 的整数 y 组成的所有数对 (x, y) 之中最长的公共前缀的长度。

返回所有数对之中最长公共前缀的长度。如果它们之间不存在公共前缀，则返回 0 。

```
impl Solution {
    pub fn longest_common_prefix(arr1: Vec<i32>, arr2: Vec<i32>) -> i32 {
        // ========== 优化点1：使用 usize 代替 i32 减少类型转换 ==========
        // Trie 节点：每个节点有 10 个子节点（数字 0-9）
        // 使用 Vec<[usize; 10]>，0 表示不存在（节省内存且避免 Option）
        let mut trie = vec![[0usize; 10]];

        // ========== 优化点2：预分配内存，减少动态扩容 ==========
        // 估算最大节点数：arr1 中所有数字的位数总和
        let max_nodes = arr1.iter().map(|&n| n.ilog10() as usize + 1).sum::<usize>() + 1;
        trie.reserve(max_nodes);

        // ========== 优化点3：提取辅助函数，提高可读性 ==========
        /// 获取数字的最高位基数（10 的幂）
        /// 例如：12345 -> 10000
        #[inline]
        fn highest_pow10(mut num: i32) -> i32 {
            let mut p = 1;
            while num >= 10 {
                num /= 10;
                p *= 10;
            }
            p
        }

        /// 遍历数字的每一位，返回迭代器
        /// 使用函数式风格，更简洁
        fn digits(mut num: i32) -> impl Iterator<Item = usize> {
            let mut div = highest_pow10(num);
            std::iter::from_fn(move || {
                if div == 0 {
                    None
                } else {
                    let digit = ((num / div) % 10) as usize;
                    div /= 10;
                    Some(digit)
                }
            })
        }

        // ========== 构建 Trie：插入 arr1 的所有数字 ==========
        for &num in &arr1 {
            let mut node = 0;
            for digit in digits(num) {
                // 获取或创建子节点
                let next = trie[node][digit];
                if next == 0 {
                    let new_idx = trie.len();
                    trie.push([0; 10]);
                    trie[node][digit] = new_idx;
                    node = new_idx;
                } else {
                    node = next;
                }
            }
        }

        // ========== 查询：在 arr2 中查找最长公共前缀 ==========
        let mut ans = 0;
        for &num in &arr2 {
            let mut node = 0;
            let mut len = 0;

            for digit in digits(num) {
                let next = trie[node][digit];
                if next == 0 {
                    break;  // 前缀中断
                }
                len += 1;
                node = next;
            }

            ans = ans.max(len);
        }

        ans
    }
}
```
