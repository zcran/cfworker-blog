---
title: "leetcode-位运算42"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 执行逐位运算使字符串相等

给你两个下标从 0 开始的 二元 字符串 s 和 target ，两个字符串的长度均为 n 。你可以对 s 执行下述操作 任意 次：

选择两个 不同 的下标 i 和 j ，其中 0 <= i, j < n 。
同时，将 s[i] 替换为 (s[i] OR s[j]) ，s[j] 替换为 (s[i] XOR s[j]) 。

例如，如果 s = "0110" ，你可以选择 i = 0 和 j = 2，然后同时将 s[0] 替换为 (s[0] OR s[2] = 0 OR 1 = 1)，并将 s[2] 替换为 (s[0] XOR s[2] = 0 XOR 1 = 1)，最终得到 s = "1110" 。

如果可以使 s 等于 target ，返回 true ，否则，返回 false 。


```
impl Solution {
    pub fn make_strings_equal(s: String, target: String) -> bool {
        // 操作本质上是在两个位置之间做可逆的位变换，不改变字符串中 '1' 的总数奇偶性之外的
        // 关键性质：只要 s 中存在至少一个 '1'，就可以通过操作生成任意非零个 '1' 的分布；
        // 因此结论是：
        //   - 若 s 全为 '0'，则只能得到全 '0'，故 target 必须全为 '0'。
        //   - 若 s 含 '1'，则可以得到任意含至少一个 '1' 的字符串，故 target 含 '1' 即可。
        let s_has_one = s.as_bytes().contains(&b'1');
        let target_has_one = target.as_bytes().contains(&b'1');
        s_has_one == target_has_one
    }
}
```
