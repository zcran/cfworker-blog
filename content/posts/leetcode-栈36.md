---
title: "leetcode-栈36"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 文件夹操作日志搜集器

每当用户执行变更文件夹操作时，LeetCode 文件系统都会保存一条日志记录。

下面给出对变更操作的说明：

"../" ：移动到当前文件夹的父文件夹。如果已经在主文件夹下，则 继续停留在当前文件夹 。
"./" ：继续停留在当前文件夹。
"x/" ：移动到名为 x 的子文件夹中。题目数据 保证总是存在文件夹 x 。

给你一个字符串列表 logs ，其中 logs[i] 是用户在 ith 步执行的操作。

文件系统启动时位于主文件夹，然后执行 logs 中的操作。

执行完所有变更文件夹操作后，请你找出 返回主文件夹所需的最小步数 。


```
impl Solution {
    pub fn min_operations(logs: Vec<String>) -> i32 {
        let mut depth = 0;

        for log in logs {
            match log.as_str() {
                "../" => {
                    if depth > 0 {
                        depth -= 1;  // 返回父目录，但不能低于根目录
                    }
                }
                "./" => {}  // 当前目录，无操作
                _ => depth += 1,  // 进入子目录
            }
        }

        depth  // 返回主文件夹需要的步数就是当前深度
    }
}
```
