---
title: "leetcode-字典树18"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---

##  删除子文件夹

你是一位系统管理员，手里有一份文件夹列表 folder，你的任务是要删除该列表中的所有 子文件夹，并以 任意顺序 返回剩下的文件夹。

如果文件夹 folder[i] 位于另一个文件夹 folder[j] 下，那么 folder[i] 就是 folder[j] 的 子文件夹 。folder[j] 的子文件夹必须以 folder[j] 开头，后跟一个 "/"。例如，"/a/b" 是 "/a" 的一个子文件夹，但 "/b" 不是 "/a/b/c" 的一个子文件夹。

文件夹的「路径」是由一个或多个按以下格式串联形成的字符串：'/' 后跟一个或者多个小写英文字母。

例如，"/leetcode" 和 "/leetcode/problems" 都是有效的路径，而空字符串和 "/" 不是。


```
impl Solution {
    pub fn remove_subfolders(mut folder: Vec<String>) -> Vec<String> {
        // 1. 对文件夹路径进行字典序排序
        //    排序后，父文件夹一定会紧挨在子文件夹前面
        //    例如：["/a", "/a/b", "/c", "/c/d", "/d"]
        folder.sort_unstable();

        let mut ans = vec![folder[0].clone()];  // 第一个路径肯定不是子文件夹

        // 2. 从第二个路径开始检查
        for s in folder.into_iter().skip(1) {
            let last = ans.last().unwrap();  // 当前结果集中最后一个路径

            // 3. 判断 s 是否是 last 的子文件夹
            //    条件1: s 必须以 last 开头
            //    条件2: s 在 last 长度后的字符必须是 '/'（确保是直接子文件夹）
            //
            //    举例说明：
            //    last = "/a", s = "/a/b/c" -> starts_with 为 true, 下一个字符是 '/' -> 是子文件夹 → 跳过
            //    last = "/a", s = "/ab"    -> starts_with 为 true, 但下一个字符是 'b' 不是 '/' -> 不是子文件夹 → 加入
            //    last = "/a/b", s = "/a/c" -> starts_with 为 false -> 不是子文件夹 → 加入
            if !s.starts_with(last) || s.as_bytes()[last.len()] != b'/' {
                ans.push(s);
            }
        }

        ans
    }
}
```
