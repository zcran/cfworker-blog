---
title: "Leetcode Shell2"
date: 2023-12-19T15:22:29+08:00
tags: ["leetcode", "Shell"]
draft: false
---

## 统计词频

写一个 bash 脚本以统计一个文本文件 words.txt 中每个单词出现的频率。

为了简单起见，你可以假设：

words.txt只包括小写字母和 ' ' 。
每个单词只由小写字母组成。
单词间由一个或多个空格字符分隔。
示例:

假设 words.txt 内容如下：

the day is sunny the the
the sunny is is
你的脚本应当输出（以词频降序排列）：

the 4
is 3
sunny 2
day 1
说明:

不要担心词频相同的单词的排序问题，每个单词出现的频率都是唯一的。
你可以使用一行 Unix pipes 实现吗？

```
cat words.txt | tr -s ' ' '\n'|sort|uniq -c |sort -r|awk '{print $2" "$1}'

cat ——浏览文件
tr -s ——替换字符串（空格换为换行）保证了一行一个单词
sort ——默认ASCII值排序，排序号后还会有重复
uniq —— 去重，-c再输出重复次数。结果就是 ”4 abc“ abc出现了4次
sort -r —— 反向排序，也就是从大到小。得到按频率高低的结果
awk ——格式化输出，规定输出是先字符串再重复次数，所以先$2再$1，中间空格分隔
```