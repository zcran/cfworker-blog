---
title: "leetcode-滚动哈希22"
date: 2026-04-17T20:00:32+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 形成目标字符串需要的最少字符串数 I

给你一个字符串数组 words 和一个字符串 target。

如果字符串 x 是 words 中 任意 字符串的 前缀，则认为 x 是一个 有效 字符串。

现计划通过 连接 有效字符串形成 target ，请你计算并返回需要连接的 最少 字符串数量。如果无法通过这种方式形成 target，则返回 -1。

```
/// 计算拼接字符串 `word + "#" + target` 的前缀函数（KMP 算法的 π 数组）
/// 返回整个数组，其中 `π[m+1+i]` 表示 target 前 i+1 个字符的后缀与 word 前缀的最长匹配长度
pub fn prefix_function(word: &str, target: &str) -> Vec<usize> {
    // 构造模式串 + 分隔符 + 文本串，确保匹配不会跨越分隔符
    let s = format!("{}#{}", word, target);
    // 获取底层字节数组，便于快速索引比较
    let bytes = s.as_bytes();
    let n = bytes.len();

    // 使用 fold 迭代器，从索引 1 开始计算前缀函数
    // 初始状态 pi = vec![0; n]，pi[0] 默认为 0
    (1..n).fold(vec![0; n], |mut pi, i| {
        // j 表示当前考虑的前缀长度（也是上一个位置的最长公共前后缀长度）
        let mut j = pi[i - 1];
        // 当 j > 0 且当前字符不匹配时，回退到更短的前缀
        while j > 0 && bytes[i] != bytes[j] {
            j = pi[j - 1];
        }
        // 若匹配成功，长度加一
        if bytes[i] == bytes[j] {
            j += 1;
        }
        // 记录当前位置的前缀函数值
        pi[i] = j;
        // 返回更新后的数组，供下一次迭代使用
        pi
    })
}

impl Solution {
    /// 计算能够拼接出 target 所需的最少单词数，若无法拼接返回 -1
    pub fn min_valid_strings(words: Vec<String>, target: String) -> i32 {
        let n = target.len();

        // 计算 back 数组：back[i] 表示以 target[i] 结尾的子串与任意单词前缀的最长匹配长度
        // 对每个单词独立计算前缀函数，然后取每个位置的最大值
        let back: Vec<usize> = words.iter()
            .map(|word| {
                // 获取当前单词的前缀函数数组
                let pi = prefix_function(word, &target);
                let m = word.len();
                // 提取 target 对应位置的最长匹配长度（跳过 word 和 '#'）
                (0..n).map(|i| pi[m + 1 + i]).collect::<Vec<_>>()
            })
            // 将所有单词的结果按位置取最大值，得到最终的 back 数组
            .fold(vec![0; n], |mut acc, cur| {
                acc.iter_mut().zip(cur).for_each(|(a, c)| *a = (*a).max(c));
                acc
            });

        // 定义无穷大值，表示不可达状态
        const INF: i32 = 1_000_000_000;
        // dp[i] 表示构造 target[..i] 所需的最少单词数，初始化为 INF
        let mut dp_init = vec![INF; n + 1];
        // 空串需要 0 个单词
        dp_init[0] = 0;

        // 使用 try_fold 进行动态规划，若遇到不可达位置则提前返回错误
        let result = (0..n).try_fold(dp_init, |mut dp, i| {
            // 仅当存在有效匹配时才更新 dp[i+1]
            if back[i] > 0 {
                // 从 i+1 - back[i] 位置的状态加上一个单词即可到达 i+1
                let prev = dp[i + 1 - back[i]];
                dp[i + 1] = prev.saturating_add(1);
            }
            // 如果 dp[i+1] 仍然超过 n（即 INF），说明当前位置无法被构造
            if dp[i + 1] > n as i32 {
                Err(())   // 短路返回错误
            } else {
                Ok(dp)    // 继续下一轮迭代
            }
        });

        // 根据结果返回最终答案或 -1
        match result {
            Ok(dp) => dp[n],   // 成功构造整个 target，返回所需单词数
            Err(()) => -1,     // 不可达，返回 -1
        }
    }
}
```



注释要点说明

段落	关键解释

prefix_function 参数与返回值	利用 KMP 前缀函数一次性计算 word 在 target 每个结尾位置的最长匹配长度。分隔符 # 避免跨边界匹配。

fold 计算前缀函数	用函数式折叠代替显式 for 循环，每次迭代返回更新后的 pi 数组，保持不可变风格。

back 数组计算	先对每个单词 map 提取对应 π 值，再 fold 逐位取最大值，得到综合所有单词的最优匹配长度。

DP 初始化	dp[0] = 0，其余为 INF，明确表示不可达。

try_fold 中的 DP 转移	if back[i] > 0 保证仅当有可用的单词前缀时才更新，避免错误地从不可达状态转移。saturating_add 防止溢出。

提前终止逻辑	dp[i+1] > n 时返回 Err(())，利用 try_fold 的短路特性立即结束迭代。

结果处理	匹配 Ok(dp) 返回 dp[n]，Err 返回 -1。
