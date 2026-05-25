---
title: "leetcode-哈希函数2"
date: 2026-04-26T20:59:32+08:00
tags: ["leetcode", "哈希函数"]
draft: false
---


## TinyURL 的加密与解密

TinyURL 是一种 URL 简化服务， 比如：当你输入一个 URL https://leetcode.com/problems/design-tinyurl 时，它将返回一个简化的URL http://tinyurl.com/4e9iAk 。请你设计一个类来加密与解密 TinyURL 。

加密和解密算法如何设计和运作是没有限制的，你只需要保证一个 URL 可以被加密成一个 TinyURL ，并且这个 TinyURL 可以用解密方法恢复成原本的 URL 。

实现 Solution 类：

Solution() 初始化 TinyURL 系统对象。
String encode(String longUrl) 返回 longUrl 对应的 TinyURL 。
String decode(String shortUrl) 返回 shortUrl 原本的 URL 。题目数据保证给定的 shortUrl 是由同一个系统对象加密的。

```
use std::collections::HashMap;

/// TinyURL 编解码器。
/// 通过自增整数计数器生成短网址，映射关系仅存储一次（解码后即删除）。
/// 注意：每个短网址只能被解码一次，第二次解码将 panic。
pub struct Codec {
    /// 下一个可用的短码整数
    next_id: usize,
    /// 从短网址（形如 "http://hi/<id>"）到原始长网址的映射
    map: HashMap<String, String>,
}

impl Codec {
    /// 创建一个新的编解码器实例。
    pub fn new() -> Self {
        Self {
            next_id: 0,
            map: HashMap::new(),
        }
    }

    /// 将长网址编码为短网址。
    /// 短网址格式：`"http://hi/"` 后跟一个递增的整数 ID。
    pub fn encode(&mut self, long_url: String) -> String {
        const PREFIX: &str = "http://hi/";
        let id = self.next_id;
        let short_url = format!("{}{}", PREFIX, id);

        // 存储映射关系
        self.map.insert(short_url.clone(), long_url);
        self.next_id += 1;

        short_url
    }

    /// 将短网址解码为原始的长网址。
    /// # Panics
    /// 如果短网址未找到或已经被解码过（因为解码后会从映射中删除），则会 panic。
    pub fn decode(&mut self, short_url: String) -> String {
        // 移除并返回对应的长网址，若不存在则 panic
        self.map
            .remove(&short_url)
            .expect("短网址不存在或已被解码")
    }
}

// 可选：实现 Default trait，使 Codec 可被直接构造
impl Default for Codec {
    fn default() -> Self {
        Self::new()
    }
}
```
