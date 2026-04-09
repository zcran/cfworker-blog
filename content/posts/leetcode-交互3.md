---
title: "leetcode-交互3"
date: 2026-03-31T20:54:46+08:00
tags: ["leetcode", "交互"]
draft: false
---


## 找出给定方程的正整数解

给你一个函数  f(x, y) 和一个目标结果 z，函数公式未知，请你计算方程 f(x,y) == z 所有可能的正整数 数对 x 和 y。满足条件的结果数对可以按任意顺序返回。

尽管函数的具体式子未知，但它是单调递增函数，也就是说：

f(x, y) < f(x + 1, y)
f(x, y) < f(x, y + 1)
函数接口定义如下：

interface CustomFunction {
public:
  // Returns some positive integer f(x, y) for two positive integers x and y based on a formula.
  int f(int x, int y);
};
你的解决方案将按如下规则进行评判：

判题程序有一个由 CustomFunction 的 9 种实现组成的列表，以及一种为特定的 z 生成所有有效数对的答案的方法。
判题程序接受两个输入：function_id（决定使用哪种实现测试你的代码）以及目标结果 z 。
判题程序将会调用你实现的 findSolution 并将你的结果与答案进行比较。
如果你的结果与答案相符，那么解决方案将被视作正确答案，即 Accepted 。

```
impl Solution {
    pub fn find_solution(fun: &CustomFunction, z: i32) -> Vec<Vec<i32>> {
        let mut res: Vec<Vec<i32>> = vec![];
        // 枚举x
        let mut y_right: i32 = 1000;
        for x in 1..=1000 {
            // y的范围为1..y_right
            loop {
                let value = fun.f(x, y_right);
                if value > z {
                    y_right -= 1;
                    if y_right == 0 {
                        return res
                    }
                } else if value == z {
                    res.push(vec![x, y_right]);
                    break;
                } else {
                    break;
                }
            }
        }
        res
    }
}
```
