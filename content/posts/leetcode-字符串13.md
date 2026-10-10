---
title: "leetcode-字符串13"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 删除注释

给一个 C++ 程序，删除程序中的注释。这个程序source是一个数组，其中source[i]表示第 i 行源码。 这表示每行源码由 '\n' 分隔。

在 C++ 中有两种注释风格，行内注释和块注释。

· 字符串// 表示行注释，表示//和其右侧的其余字符应该被忽略。
· 字符串/* 表示一个块注释，它表示直到下一个（非重叠）出现的*/之间的所有字符都应该被忽略。（阅读顺序为从左到右）非重叠是指，字符串/*/并没有结束块注释，因为注释的结尾与开头相重叠。

第一个有效注释优先于其他注释。

· 如果字符串//出现在块注释中会被忽略。
· 同样，如果字符串/*出现在行或块注释中也会被忽略。

如果一行在删除注释之后变为空字符串，那么不要输出该行。即，答案列表中的每个字符串都是非空的。

样例中没有控制字符，单引号或双引号字符。

· 比如，source = "string s = "/* Not a comment. */";" 不会出现在测试样例里。

此外，没有其他内容（如定义或宏）会干扰注释。

我们保证每一个块注释最终都会被闭合， 所以在行或块注释之外的/*总是开始新的注释。

最后，隐式换行符可以通过块注释删除。 有关详细信息，请参阅下面的示例。

从源代码中删除注释后，需要以相同的格式返回源代码。


```
impl Solution {
    pub fn remove_comments(source: Vec<String>) -> Vec<String> {
        let mut ans: Vec<String> = Vec::new();
        let mut cur = String::new();
        let mut in_block = false;

        for line in &source {
            let bytes = line.as_bytes();
            let m = bytes.len();
            let mut i = 0;

            while i < m {
                if in_block {
                    // 块注释中：寻找结束标记 "*/"
                    if i + 1 < m && bytes[i] == b'*' && bytes[i + 1] == b'/' {
                        in_block = false;
                        i += 2;
                    } else {
                        i += 1;
                    }
                } else if i + 1 < m && bytes[i] == b'/' && bytes[i + 1] == b'*' {
                    // 遇到 "/*"：进入块注释
                    in_block = true;
                    i += 2;
                } else if i + 1 < m && bytes[i] == b'/' && bytes[i + 1] == b'/' {
                    // 遇到 "//"：本行剩余部分全部忽略
                    break;
                } else {
                    // 普通字符：追加到当前行缓冲
                    cur.push(bytes[i] as char);
                    i += 1;
                }
            }

            // 块注释外的行结束时，若缓冲非空则收集为一行（隐式换行被块注释吞掉时不收集）
            if !in_block && !cur.is_empty() {
                ans.push(std::mem::take(&mut cur));
            }
        }

        ans
    }
}
```
