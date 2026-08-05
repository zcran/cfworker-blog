---
title: "leetcode-设计36"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 运算

请实现整数数字的乘法、减法和除法运算，运算结果均为整数数字，程序中只允许使用加法运算符和逻辑运算符，允许程序中出现正负常数，不允许使用位运算。

你的实现应该支持如下操作：

Operations() 构造函数
minus(a, b) 减法，返回a - b
multiply(a, b) 乘法，返回a * b
divide(a, b) 除法，返回a / b


```
/// 32位整数模拟器（仅使用加法运算）
///
/// 使用布尔数组表示二进制补码，实现整数运算
/// 所有底层操作仅依赖加法，满足题目约束
struct I32 {
    bits: [bool; 32], // 二进制补码表示，bits[0]为符号位
}

impl I32 {
    /// 2的幂次常量表（用于二进制转换）
    const TWO_POW: [i32; 31] = [
        0x40000000, 0x20000000, 0x10000000, 0x08000000, 0x04000000, 0x02000000, 0x01000000,
        0x00800000, 0x00400000, 0x00200000, 0x00100000, 0x00080000, 0x00040000, 0x00020000,
        0x00010000, 0x00008000, 0x00004000, 0x00002000, 0x00001000, 0x00000800, 0x00000400,
        0x00000200, 0x00000100, 0x00000080, 0x00000040, 0x00000020, 0x00000010, 0x00000008,
        0x00000004, 0x00000002, 0x00000001,
    ];

    /// 常量 1（二进制表示）
    const ONE: Self = Self {
        bits: [
            false, false, false, false, false, false, false, false, false, false,
            false, false, false, false, false, false, false, false, false, false,
            false, false, false, false, false, false, false, false, false, false,
            false, true, // 最低位为1
        ],
    };

    /// 常量 0（所有位为false）
    const ZERO: Self = Self { bits: [false; 32] };

    /// 从 i32 转换为二进制位数组
    pub fn from_i32(mut value: i32) -> Self {
        let mut bits = [false; 32];

        // 处理负数：转换为补码表示
        if value < 0 {
            // 负数补码：将负数加到 2^31 范围
            value += Self::TWO_POW[0];
            value += Self::TWO_POW[0];
            bits[0] = true; // 设置符号位
        }

        // 将数值转换为二进制（从高位到低位）
        let mut accumulated = 0;
        for i in 0..31 {
            let candidate = accumulated + Self::TWO_POW[i];
            if candidate <= value {
                accumulated = candidate;
                bits[i + 1] = true;
            }
        }
        Self { bits }
    }

    /// 从二进制位数组转换为 i32
    pub fn to_i32(&self) -> i32 {
        let mut result = 0;

        // 计算绝对值部分
        for i in 0..31 {
            if self.bits[i + 1] {
                result += Self::TWO_POW[i];
            }
        }

        // 处理负数：从 2^31 范围减去
        if self.bits[0] {
            result -= Self::TWO_POW[0];
            result -= Self::TWO_POW[0];
        }
        result
    }

    /// 二进制加法（位数组操作）
    ///
    /// # 参数
    /// - `a`: 被加数（同时也是结果存储位置）
    /// - `b`: 加数（长度不超过 a）
    fn vec_add(a: &mut [bool], b: &[bool]) {
        let mut carry = 0u8; // 进位标志

        // 从最低位（右侧）开始逐位相加
        for (a_bit, b_bit) in a.iter_mut().rev().zip(b.iter().rev()) {
            // 计算当前位的和（包含进位）
            carry += match (*a_bit, *b_bit) {
                (true, true) => 2,       // 1 + 1 = 2
                (true, false) | (false, true) => 1, // 1 + 0 = 1
                (false, false) => 0,     // 0 + 0 = 0
            };

            // 更新当前位和进位
            (*a_bit, carry) = match carry {
                0 => (false, 0), // 和为0，进位0
                1 => (true, 0),  // 和为1，进位0
                2 => (false, 1), // 和为0，进位1
                _ => (true, 1),  // 和为1，进位1
            }
        }
    }

    /// 加法运算（对外接口）
    pub fn add(&self, other: &Self) -> Self {
        let mut bits = self.bits.clone();
        Self::vec_add(&mut bits, &other.bits);
        I32 { bits }
    }

    /// 取反加一（求补码）
    ///
    /// 实现：按位取反后加1
    fn complement(bits: &mut [bool]) {
        // 按位取反
        for bit in bits.iter_mut() {
            *bit = !*bit;
        }
        // 加1
        Self::vec_add(bits, &Self::ONE.bits);
    }

    /// 取负值
    pub fn neg(&self) -> Self {
        let mut bits = self.bits.clone();
        Self::complement(&mut bits);
        Self { bits }
    }

    /// 取绝对值
    pub fn abs(&self) -> Self {
        if self.bits[0] {
            self.neg() // 负数取反
        } else {
            I32 {
                bits: self.bits.clone(),
            }
        }
    }

    /// 减法：a - b = a + (-b)
    pub fn minus(&self, other: &Self) -> Self {
        let mut bits = other.bits.clone();
        Self::complement(&mut bits); // 取 b 的负数
        Self::vec_add(&mut bits, &self.bits); // a + (-b)
        Self { bits }
    }

    /// 乘法：Booth 算法
    ///
    /// 通过检查相邻两位决定加/减被乘数
    pub fn multiply(&self, other: &Self) -> Self {
        let mut product = [false; 32]; // 乘积寄存器
        let mut multiplier = [false; 33]; // 乘数寄存器（扩展1位）
        multiplier[0..32].copy_from_slice(&self.bits);

        let multiplicand = other.bits;
        let mut multiplicand_neg = multiplicand.clone();
        Self::complement(&mut multiplicand_neg); // 被乘数的负数

        // 从低位到高位遍历乘数的每一位
        for (i, j) in (0..32).rev().zip(0..32) {
            match (multiplier[i], multiplier[i + 1]) {
                (true, false) => {
                    // 10 模式：加被乘数
                    Self::vec_add(&mut product[0..=i], &multiplicand_neg[j..32]);
                }
                (false, true) => {
                    // 01 模式：减被乘数（加负数）
                    Self::vec_add(&mut product[0..=i], &multiplicand[j..32]);
                }
                _ => {} // 00 或 11：无操作
            }
        }
        Self { bits: product }
    }

    /// 除法：恢复余数法
    pub fn divide_by(&self, other: &Self) -> Self {
        // 处理除零错误
        if other.bits == Self::ZERO.bits {
            panic!("attempt to divide by zero");
        }

        // 被除数为0直接返回
        if self.bits == Self::ZERO.bits {
            return Self { bits: [false; 32] };
        }

        let mut dividend = self.bits.clone();
        let mut divisor = other.bits.clone();
        let mut result = [false; 32];

        // 确定结果符号
        let result_negative = match (dividend[0], divisor[0]) {
            (true, true) => {
                // 两个负数：取绝对值，结果为正
                Self::complement(&mut dividend);
                Self::complement(&mut divisor);
                false
            }
            (true, false) => {
                // 被除数为负，除数为正：结果为负
                Self::complement(&mut dividend);
                true
            }
            (false, true) => {
                // 被除数为正，除数为负：结果为负
                Self::complement(&mut divisor);
                true
            }
            (false, false) => false, // 两个正数：结果为正
        };

        // 找到被除数的最高有效位
        let mut msb_dividend = 0;
        while msb_dividend < 32 && !dividend[msb_dividend] {
            msb_dividend += 1;
        }

        // 找到除数的最高有效位
        let mut msb_divisor = 0;
        while msb_divisor < 32 && !divisor[msb_divisor] {
            msb_divisor += 1;
        }

        // 如果除数大于被除数，结果为0
        if msb_divisor >= msb_dividend {
            // 检查被除数是否小于除数
            fn is_less(a: &[bool; 32], b: &[bool; 32]) -> bool {
                for (a_bit, b_bit) in a.iter().zip(b) {
                    match (a_bit, b_bit) {
                        (true, true) | (false, false) => continue,
                        (true, false) => return false, // a > b
                        (false, true) => return true,  // a < b
                    }
                }
                true // 相等
            }

            let mut current = [false; 32];
            let mut temp = [false; 32];
            let mut has_result = false;

            // 从高位到低位尝试每一位
            let shift_range = msb_divisor - msb_dividend;
            for offset in (0..=shift_range).rev() {
                let bit_pos = 31 - offset;
                Self::vec_add(&mut temp[0..=bit_pos], &divisor[offset..]);

                if is_less(&temp, &dividend) {
                    has_result = true;
                    result[bit_pos] = true;
                    current.copy_from_slice(&temp);
                } else {
                    temp.copy_from_slice(&current);
                }
            }

            // 如果结果为负，取补码
            if has_result && result_negative {
                Self::complement(&mut result);
            }
        }

        Self { bits: result }
    }
}

/// 运算操作器（外部接口）
struct Operations {}

impl Operations {
    /// 创建新的操作器实例
    fn new() -> Self {
        Operations {}
    }

    /// 减法：a - b
    fn minus(&self, a: i32, b: i32) -> i32 {
        I32::from_i32(a).minus(&I32::from_i32(b)).to_i32()
    }

    /// 乘法：a * b（使用 Booth 算法）
    fn multiply(&self, a: i32, b: i32) -> i32 {
        I32::from_i32(a).multiply(&I32::from_i32(b)).to_i32()
    }

    /// 除法：a / b（向零取整）
    fn divide(&self, a: i32, b: i32) -> i32 {
        I32::from_i32(a).divide_by(&I32::from_i32(b)).to_i32()
    }
}
```
