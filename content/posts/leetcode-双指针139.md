---
title: "leetcode-双指针139"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 动态口令

某公司门禁密码使用动态口令技术。初始密码为字符串 password，密码更新均遵循以下步骤：

设定一个正整数目标值 target

将 password 前 target 个字符按原顺序移动至字符串末尾

请返回更新后的密码字符串。


```
impl Solution {
    pub fn dynamic_password(password: String, target: i32) -> String {
        let target = target as usize;
        // 前 n 个字符移到末尾：等价于先拼接 n.. 再拼接 ..n
        // 用 into_bytes 避免 UTF-8 边界检查，再安全转回 String
        let mut bytes = password.into_bytes();
        bytes.rotate_left(target);
        String::from_utf8(bytes).unwrap()
    }
}
```
