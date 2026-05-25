---
title: "leetcode-树状树组18"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 最长上传前缀


给你一个 n 个视频的上传序列，每个视频编号为 1 到 n 之间的 不同 数字，你需要依次将这些视频上传到服务器。请你实现一个数据结构，在上传的过程中计算 最长上传前缀 。

如果 闭区间 1 到 i 之间的视频全部都已经被上传到服务器，那么我们称 i 是上传前缀。最长上传前缀指的是符合定义的 i 中的 最大值 。

请你实现 LUPrefix 类：

LUPrefix(int n) 初始化一个 n 个视频的流对象。
void upload(int video) 上传 video 到服务器。
int longest() 返回上述定义的 最长上传前缀 的长度。

```/// 管理视频上传的前缀状态，支持：
/// - `upload(video)`：标记视频已上传
/// - `longest()`：返回当前最长的连续已上传前缀长度（从1开始）
struct LUPrefix {
    uploaded: Vec<bool>, // 标记视频是否已上传，下标从1开始（索引0未使用）
    longest: i32,        // 当前最长连续前缀长度
}

impl LUPrefix {
    /// 创建一个能处理 `1..=n` 个视频的 LUPrefix 实例
    pub fn new(n: i32) -> Self {
        Self {
            uploaded: vec![false; n as usize + 1], // 下标0占位，确保索引从1开始
            longest: 0,
        }
    }

    /// 上传指定编号的视频
    /// 算法：标记对应位置后，尝试扩展最长连续前缀
    pub fn upload(&mut self, video: i32) {
        let idx = video as usize;
        self.uploaded[idx] = true;

        // 从当前最长前缀的下一个位置开始，检查是否连续上传
        let mut next = (self.longest + 1) as usize;
        while next < self.uploaded.len() && self.uploaded[next] {
            next += 1;
        }
        // 更新最长前缀为最后检查到的连续位置的前一个索引
        self.longest = (next - 1) as i32;
    }

    /// 返回当前最长的连续已上传前缀长度
    pub fn longest(&self) -> i32 {
        self.longest
    }
}
```
