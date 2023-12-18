---
title: "Rust Note 20"
date: 2023-05-22T20:28:46+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
## 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

### C 解法：
```
 // 先排序，后比较头尾
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if(strs.empty()) return string();
        sort(strs.begin(), strs.end());
        string st = strs.front(), en = strs.back();
        int i, num = min(st.size(), en.size());
        for(i = 0; i < num && st[i] == en[i]; i ++);
        return string(st, 0, i);
    }
};
```


### Rust 解法1：
```
impl Solution {
    pub fn longest_common_prefix(strs: Vec<String>) -> String {
        let mut x:Vec<_> = strs.iter().map(|c|c.chars()).collect(); 
        let mut res = String::new();
        while let Some(t) = x[0].next() {
            for i in &mut x[1..] {
                if let Some(y) = i.next() {
                    if (t != y) {
                        return res;
                    }
                }   else {
                    return res;
                }
            }
            res.push(t)
        }
        res
    }
}
```

### Rust解法2:
```
impl Solution {
    pub fn longest_common_prefix(strs: Vec<String>) -> String {
        strs.iter()
            .max()
            .unwrap()
            .chars()
            .zip(strs.iter().min().unwrap().chars())
            .take_while(|x| x.0 == x.1)
            .map(|x| x.0)
            .collect()
    }
}
```

以下是chatgpt给出的解释：
```
这是一个Rust语言实现的函数，用于找出给定字符串数组中最长的公共前缀。具体实现步骤如下：

通过iter()方法将字符串数组转化为迭代器；
利用max()方法将字符串数组中最长的字符串取出；
利用unwrap()方法获取最长的字符串的值；
对最长的字符串调用chars()方法，返回一个Unicode字符迭代器，在下一步与其他字符串的字符逐一比较；
提取其他字符串中的最小值，使用min()方法获得并获取其中的值；
将最长字符串的每个字符与其他字符串的每个字符进行逐一比较，使用zip()方法将两个字符迭代器进行连接；
使用take_while()方法遍历两个字符迭代器，并取出它们相同的部分；
对于每一组相同的字符，使用map()方法将它们的第一个字符添加到新建Vector的末尾；
返回新建Vector中的所有字符，作为最长公共前缀字符串。
```