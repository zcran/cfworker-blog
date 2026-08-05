---
title: "leetcode-栈15"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 函数的独占时间

有一个 单线程 CPU 正在运行一个含有 n 道函数的程序。每道函数都有一个位于 0 和 n-1 之间的唯一标识符。

函数调用 存储在一个 调用栈 上 ：当一个函数调用开始时，它的标识符将会推入栈中。而当一个函数调用结束时，它的标识符将会从栈中弹出。标识符位于栈顶的函数是 当前正在执行的函数 。每当一个函数开始或者结束时，将会记录一条日志，包括函数标识符、是开始还是结束、以及相应的时间戳。

给你一个由日志组成的列表 logs ，其中 logs[i] 表示第 i 条日志消息，该消息是一个按 "{function_id}:{"start" | "end"}:{timestamp}" 进行格式化的字符串。例如，"0:start:3" 意味着标识符为 0 的函数调用在时间戳 3 的 起始开始执行 ；而 "1:end:2" 意味着标识符为 1 的函数调用在时间戳 2 的 末尾结束执行。注意，函数可以 调用多次，可能存在递归调用 。

函数的 独占时间 定义是在这个函数在程序所有函数调用中执行时间的总和，调用其他函数花费的时间不算该函数的独占时间。例如，如果一个函数被调用两次，一次调用执行 2 单位时间，另一次调用执行 1 单位时间，那么该函数的 独占时间 为 2 + 1 = 3 。

以数组形式返回每个函数的 独占时间 ，其中第 i 个下标对应的值表示标识符 i 的函数的独占时间。


```
// 函数独占时间计算
// 使用栈跟踪调用：每个栈元素存储 (开始时间, 已占用时间偏移)
impl Solution {
    pub fn exclusive_time(n: i32, logs: Vec<String>) -> Vec<i32> {
        let mut result = vec![0; n as usize];
        let mut stack = Vec::with_capacity(n as usize);

        for log in logs {
            // 解析日志：id, 是否开始, 时间戳
            let parts: Vec<&str> = log.split(':').collect();
            let id = parts[0].parse::<usize>().unwrap();
            let is_start = parts[1] == "start";
            let timestamp = parts[2].parse::<i32>().unwrap();

            if is_start {
                // 开始：记录开始时间，初始偏移为 0
                stack.push((timestamp, 0));
            } else {
                // 结束：计算当前函数的执行时间
                let (start_time, offset) = stack.pop().unwrap();
                let duration = timestamp - start_time + 1 - offset;
                result[id] += duration;

                // 如果栈非空，更新父函数的时间偏移
                if let Some(parent) = stack.last_mut() {
                    parent.1 += timestamp - start_time + 1;
                }
            }
        }

        result
    }
}
```
