---
title: "leetcode-滚动哈希20"
date: 2026-04-17T20:00:32+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 统计前后缀下标对 I

给你一个下标从 0 开始的字符串数组 words 。

定义一个 布尔 函数 isPrefixAndSuffix ，它接受两个字符串参数 str1 和 str2 ：

当 str1 同时是 str2 的前缀（prefix）和后缀（suffix）时，isPrefixAndSuffix(str1, str2) 返回 true，否则返回 false。
例如，isPrefixAndSuffix("aba", "ababa") 返回 true，因为 "aba" 既是 "ababa" 的前缀，也是 "ababa" 的后缀，但是 isPrefixAndSuffix("abc", "abcd") 返回 false。

以整数形式，返回满足 i < j 且 isPrefixAndSuffix(words[i], words[j]) 为 true 的下标对 (i, j) 的 数量 。


```
impl Solution {
    // 定义方法：接收 String 向量，返回满足条件的对数 (i32)
    pub fn count_prefix_suffix_pairs(words: Vec<String>) -> i32 {
        // 开始函数式迭代链
        words
            .iter()          // 1. 获取 words 的不可变迭代器，元素类型为 &String
            .enumerate()     // 2. 给每个元素附上索引，产生 (usize, &String) 元组
            .flat_map(|(i, s1)| {   // 3. 对于每个 (索引i, 字符串s1)，展开为多个 (s1, s2) 对
                // 4. 取 words 中索引从 i+1 到末尾的子切片，并迭代其引用
                words[i + 1..].iter()
                    // 5. 将当前 s1 与子切片中的每个 s2 配对，move 将 s1 的所有权移入闭包（s1 是引用，复制的是引用）
                    .map(move |s2| (s1, s2))
            })
            // 6. 过滤：只保留满足 s2 同时以 s1 开头和结尾的配对
            .filter(|(s1, s2)| s2.starts_with(*s1) && s2.ends_with(*s1))
            // 7. 统计满足条件的配对数量，并转换为 i32 类型作为返回值
            .count() as i32
    }
}
```




逐行详解

words.iter()
返回一个迭代器，每次产生 &String（对向量中字符串的不可变引用）。避免消耗所有权。
.enumerate()
将迭代器的每个元素包裹上索引，产生 (usize, &String)。例如第一个元素为 (0, &words[0])，用于后续限制 j > i。
.flat_map(|(i, s1)| { ... })

flat_map 用于将每个输入元素映射为一个迭代器，然后将所有迭代器展平成一个迭代器。
闭包参数 (i, s1) 解构索引和字符串引用。
返回一个迭代器，产生多个 (s1, s2) 对，其中 s2 的索引必须大于 i。
words[i + 1..].iter()

使用范围索引 i+1.. 创建从 i+1 到末尾的切片（不包含 i 本身）。
调用 .iter() 获得该切片的迭代器，元素类型为 &String。
.map(move |s2| (s1, s2))

将每个 s2（&String）与当前外层的 s1 配对成元组 (s1, s2)。
move 关键字将闭包中使用的 s1 从外层环境移动进来（这里 s1 是 &String，移动的只是引用，不影响原所有权）。
结果是一个迭代器，元素为 (&String, &String)。
.filter(|(s1, s2)| s2.starts_with(*s1) && s2.ends_with(*s1))

filter 保留闭包返回 true 的元素。
闭包参数解构为 (s1, s2)，它们都是 &&String 类型？实际上：

s1 来自 flat_map 中的 s1，它是 &String。
s2 来自切片迭代器，也是 &String。
但在 flat_map 返回的元组中，类型为 (&String, &String)，所以 filter 接收的是 (&String, &String) 的引用（因为迭代器产生元组的引用？）。
实际上，.flat_map 返回的迭代器直接产生 (&String, &String) 值，因此 filter 闭包接收的是 &(&String, &String)，解构后 s1 和 s2 的类型为 &&String。
因此需要 *s1 解引用一次，变成 &String，才能作为 starts_with 和 ends_with 的参数（这两个方法要求参数实现 Pattern，&String 实现了该 trait）。
条件：s2.starts_with(*s1) 判断 s2 是否以 s1 为前缀；s2.ends_with(*s1) 判断是否以 s1 为后缀。
.count() as i32

count() 消耗迭代器，返回满足条件的元素个数（usize 类型）。
使用 as i32 转换为方法声明的返回类型 i32。
关键点说明

move 的作用：确保 s1 被移动到 map 闭包中，因为闭包的生命周期可能长于 flat_map 的迭代过程。由于 s1 是引用，move 只是复制引用，不会导致所有权问题。
解引用 *s1：因为 filter 闭包中 s1 的类型是 &&String，需要解引用一层变成 &String 才能传递给 starts_with。也可以写作 s2.starts_with(s1) 但会报错（类型不匹配），正确写法是 s2.starts_with(&s1)? 不，s1 已经是 &&String，取地址会更乱，直接用 *s1 最清晰。
时间复杂度：O(n²) 的字符串比较，最坏情况每个 s2 都要扫描前后缀，但符合原双重循环的语义。
