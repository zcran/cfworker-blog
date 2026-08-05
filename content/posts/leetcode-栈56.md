---
title: "leetcode-栈56"
date: 2026-07-24T10:17:00+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 计算字符串的镜像分数

给你一个字符串 s。

英文字母中每个字母的 镜像 定义为反转字母表之后对应位置上的字母。例如，'a' 的镜像是 'z'，'y' 的镜像是 'b'。

最初，字符串 s 中的所有字符都 未标记 。

字符串 s 的初始分数为 0 ，你需要对其执行以下过程：

· 从左到右遍历字符串。
· 对于每个下标 i ，找到距离最近的 未标记 下标 j，下标 j 需要满足 j < i 且 s[j] 是 s[i] 的镜像。然后 标记 下标 i 和 j，总分加上 i - j 的值。
· 如果对于下标 i，不存在满足条件的下标 j，则跳过该下标，继续处理下一个下标，不需要进行标记。

返回最终的总分。


```
impl Solution {
    /// 返回字符串 s 的镜像配对总分
    ///
    /// # 算法思路
    /// 使用 26 个栈，存储每个字母出现的位置索引：
    /// - 遍历字符串，当前字符为 c
    /// - 如果栈 c 不为空，说明之前有未配对的 c，弹出并配对（镜像配对要求 c 与 'a'+25-c 配对）
    /// - 如果栈 c 为空，说明当前字符需要等待其镜像字符，将当前位置压入栈 25-c
    /// - 配对时分数加上距离差 i - j
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)，一次遍历
    /// - 空间复杂度：O(n)，栈存储位置索引
    pub fn calculate_score(s: String) -> i64 {
        // 26 个栈，存储每个字母出现的位置
        // positions[0] 存储 'a' 的位置，positions[1] 存储 'b' 的位置，以此类推
        let mut positions: [Vec<usize>; 26] = std::array::from_fn(|_| Vec::new());
        let mut total_score = 0i64;

        for (i, ch) in s.bytes().enumerate() {
            let c = (ch - b'a') as usize;

            // 检查栈 c 是否有未配对的相同字符
            if let Some(j) = positions[c].pop() {
                // 当前字符与之前未配对的相同字符配对
                // 距离为 i - j（i > j 因为从左到右遍历）
                total_score += (i - j) as i64;
            } else {
                // 没有未配对的相同字符，当前字符等待其镜像字符
                let mirror = 25 - c;
                positions[mirror].push(i);
            }
        }

        total_score
    }
}
```
