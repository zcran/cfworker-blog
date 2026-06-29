---
title: "leetcode-数论18"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 替换数组中的非互质数

给你一个整数数组 nums 。请你对数组执行下述操作：

1.从 nums 中找出 任意 两个 相邻 的 非互质 数。
2.如果不存在这样的数，终止 这一过程。
3.否则，删除这两个数，并 替换 为它们的 最小公倍数（Least Common Multiple，LCM）。
4.只要还能找出两个相邻的非互质数就继续 重复 这一过程。

返回修改后得到的 最终 数组。可以证明的是，以 任意 顺序替换相邻的非互质数都可以得到相同的结果。

生成的测试用例可以保证最终数组中的值 小于或者等于 108 。

两个数字 x 和 y 满足 非互质数 的条件是：GCD(x, y) > 1 ，其中 GCD(x, y) 是 x 和 y 的 最大公约数 。


```
impl Solution {
    pub fn replace_non_coprimes(nums: Vec<i32>) -> Vec<i32> {
        let mut stack = Vec::with_capacity(nums.len());

        for num in nums {
            let mut cur = num;

            // 不断与栈顶合并，直到不能合并为止
            while let Some(&top) = stack.last() {
                let g = gcd(top, cur);
                if g == 1 {
                    break;
                }
                // 计算 LCM: a * b / gcd(a, b)
                cur = top / g * cur;
                stack.pop();
            }

            stack.push(cur);
        }

        stack
    }
}

/// 欧几里得算法计算最大公约数（迭代版本）
#[inline]
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a
}
```
