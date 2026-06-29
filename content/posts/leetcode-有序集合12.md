---
title: "leetcode-有序集合12"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 推文计数

一家社交媒体公司正试图通过分析特定时间段内出现的推文数量来监控其网站上的活动。这些时间段可以根据特定的频率（ 每分钟 、每小时 或 每一天 ）划分为更小的 时间段 。



例如，周期 [10,10000] （以 秒 为单位）将被划分为以下频率的 时间块 :

· 每 分钟 (60秒 块)： [10,69], [70,129], [130,189], ..., [9970,10000]
· 每 小时 (3600秒 块)：[10,3609], [3610,7209], [7210,10000]
· 每 天 (86400秒 块)： [10,10000]

注意，最后一个块可能比指定频率的块大小更短，并且总是以时间段的结束时间结束(在上面的示例中为 10000 )。

设计和实现一个API来帮助公司进行分析。

实现 TweetCounts 类:

· TweetCounts() 初始化 TweetCounts 对象。
· 存储记录时间的 tweetName (以秒为单位)。
· List<integer> getTweetCountsPerFrequency(String freq, String tweetName, int startTime, int endTime) 返回一个整数列表，表示给定时间 [startTime, endTime] （单位秒）和频率频率中，每个 时间块 中带有 tweetName 的 tweet 的数量。
    · freq 是 “minute” 、 “hour” 或 “day” 中的一个，分别表示 每分钟 、 每小时 或 每一天 的频率。


```
use std::collections::HashMap;

/// 推文计数API，用于分析特定时间段内的推文数量
#[derive(Default)]
pub struct TweetCounts {
    /// 用户名 -> 推文时间戳列表（秒）
    tweets: HashMap<String, Vec<i32>>,
}

impl TweetCounts {
    /// 创建一个新的TweetCounts实例
    pub fn new() -> Self {
        Self::default()
    }

    /// 记录一条推文
    ///
    /// # 参数
    /// - `tweet_name`: 推文名称/用户名
    /// - `time`: 推文发布时间（秒）
    ///
    /// # 时间复杂度
    /// O(1) 平均
    pub fn record_tweet(&mut self, tweet_name: String, time: i32) {
        self.tweets.entry(tweet_name).or_default().push(time);
    }

    /// 获取指定频率下每个时间块的推文数量
    ///
    /// # 参数
    /// - `freq`: 频率，支持 "minute"、"hour"、"day"
    /// - `tweet_name`: 推文名称
    /// - `start_time`: 开始时间（秒）
    /// - `end_time`: 结束时间（秒）
    ///
    /// # 返回值
    /// 每个时间块中的推文数量列表
    ///
    /// # 时间复杂度
    /// O(n)，其中n是该用户的推文数量
    pub fn get_tweet_counts_per_frequency(
        &self,
        freq: String,
        tweet_name: String,
        start_time: i32,
        end_time: i32,
    ) -> Vec<i32> {
        // 1. 确定时间块大小（秒）
        let block_size = match freq.as_str() {
            "minute" => 60,
            "hour" => 3600,
            "day" => 86400,
            _ => panic!("Invalid frequency: {}", freq),
        };

        // 2. 计算需要的块数（包含边界）
        let total_blocks = ((end_time - start_time) / block_size) + 1;
        let mut result = vec![0; total_blocks as usize];

        // 3. 获取该用户的推文列表
        let Some(times) = self.tweets.get(&tweet_name) else {
            return result;
        };

        // 4. 只处理在时间范围内的推文
        for &time in times {
            if time >= start_time && time <= end_time {
                let block_index = ((time - start_time) / block_size) as usize;
                result[block_index] += 1;
            }
        }

        result
    }

    /// 获取某个用户的总推文数
    pub fn tweet_count(&self, tweet_name: &str) -> usize {
        self.tweets.get(tweet_name).map_or(0, Vec::len)
    }

    /// 清除某个用户的所有推文
    pub fn clear_tweets(&mut self, tweet_name: &str) {
        self.tweets.remove(tweet_name);
    }
}
```
