---
title: "leetcode-设计28"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 设计位集

位集 Bitset 是一种能以紧凑形式存储位的数据结构。

请你实现 Bitset 类。

Bitset(int size) 用 size 个位初始化 Bitset ，所有位都是 0 。
void fix(int idx) 将下标为 idx 的位上的值更新为 1 。如果值已经是 1 ，则不会发生任何改变。
void unfix(int idx) 将下标为 idx 的位上的值更新为 0 。如果值已经是 0 ，则不会发生任何改变。
void flip() 翻转 Bitset 中每一位上的值。换句话说，所有值为 0 的位将会变成 1 ，反之亦然。
boolean all() 检查 Bitset 中 每一位 的值是否都是 1 。如果满足此条件，返回 true ；否则，返回 false 。
boolean one() 检查 Bitset 中 是否 至少一位 的值是 1 。如果满足此条件，返回 true ；否则，返回 false 。
int count() 返回 Bitset 中值为 1 的位的 总数 。
String toString() 返回 Bitset 的当前组成情况。注意，在结果字符串中，第 i 个下标处的字符应该与 Bitset 中的第 i 位一致。


```
/// 位集（Bitset）数据结构
///
/// 使用惰性翻转优化：记录翻转状态，避免实际翻转数组
/// 时间复杂度: 所有操作 O(1)，toString O(n)
/// 空间复杂度: O(n)
struct Bitset {
    bits: Vec<bool>,    // 存储实际位值（逻辑值可能被翻转）
    ones: usize,        // 逻辑值为 1 的位数
    flipped: bool,      // 是否处于翻转状态
    size: usize,        // 总位数
}

impl Bitset {
    /// 初始化 size 位，全部为 0
    fn new(size: i32) -> Self {
        let size = size as usize;
        Bitset {
            bits: vec![false; size],
            ones: 0,
            flipped: false,
            size,
        }
    }

    /// 将第 idx 位设为 1
    fn fix(&mut self, idx: i32) {
        let idx = idx as usize;
        let old = self.bits[idx];

        // 计算逻辑值：如果 flipped 为 true，实际值取反
        let logical = old ^ self.flipped;

        // 只有当逻辑值为 0 时才需要修改
        if !logical {
            self.bits[idx] = !old; // 将实际值取反
            self.ones += 1;        // 逻辑 1 的数量增加
        }
    }

    /// 将第 idx 位设为 0
    fn unfix(&mut self, idx: i32) {
        let idx = idx as usize;
        let old = self.bits[idx];
        let logical = old ^ self.flipped;

        // 只有当逻辑值为 1 时才需要修改
        if logical {
            self.bits[idx] = !old;
            self.ones -= 1;
        }
    }

    /// 翻转所有位
    fn flip(&mut self) {
        self.flipped = !self.flipped;
        self.ones = self.size - self.ones;
    }

    /// 检查是否所有位都为 1
    fn all(&self) -> bool {
        self.ones == self.size
    }

    /// 检查是否至少有一位为 1
    fn one(&self) -> bool {
        self.ones > 0
    }

    /// 返回值为 1 的位数
    fn count(&self) -> i32 {
        self.ones as i32
    }

    /// 返回位集的字符串表示
    fn to_string(&self) -> String {
        // 根据翻转状态决定如何显示
        let zero_char = if self.flipped { '1' } else { '0' };
        let one_char = if self.flipped { '0' } else { '1' };

        self.bits
            .iter()
            .map(|&b| if b { one_char } else { zero_char })
            .collect()
    }
}
```
