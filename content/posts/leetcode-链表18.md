---
title: "leetcode-链表18"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 设计推特

设计一个简化版的推特(Twitter)，可以让用户实现发送推文，关注/取消关注其他用户，能够看见关注人（包括自己）的最近 10 条推文。

实现 Twitter 类：

· Twitter() 初始化简易版推特对象

· void postTweet(int userId, int tweetId) 根据给定的 tweetId 和 userId 创建一条新推文。每次调用此函数都会使用一个不同的 tweetId 。

· List<Integer> getNewsFeed(int userId) 检索当前用户新闻推送中最近  10 条推文的 ID 。新闻推送中的每一项都必须是由用户关注的人或者是用户自己发布的推文。推文必须 按照时间顺序由最近到最远排序 。

· void follow(int followerId, int followeeId) ID 为 followerId 的用户开始关注 ID 为 followeeId 的用户。

· void unfollow(int followerId, int followeeId) ID 为 followerId 的用户不再关注 ID 为 followeeId 的用户。


```
use std::collections::{HashMap, HashSet};
use std::cmp::Reverse;

/// 用户信息
struct User {
    followers: HashSet<i32>,  // 关注该用户的用户集合
    tweets: Vec<i32>,         // 该用户发布的推文 ID（按时间倒序）
}

impl User {
    fn new() -> Self {
        User {
            followers: HashSet::new(),
            tweets: Vec::new(),
        }
    }

    /// 添加关注者
    fn add_follower(&mut self, user_id: i32) {
        self.followers.insert(user_id);
    }

    /// 移除关注者
    fn remove_follower(&mut self, user_id: i32) {
        self.followers.remove(&user_id);
    }

    /// 发布推文（插入到最前面）
    fn post_tweet(&mut self, tweet_id: i32) {
        self.tweets.insert(0, tweet_id);
        // 只保留最近 10 条推文
        if self.tweets.len() > 10 {
            self.tweets.pop();
        }
    }
}

/// 简化版推特
///
/// 支持：发布推文、关注/取消关注、获取新闻推送
/// 使用 HashMap 存储用户信息，推文按时间倒序排列
pub struct Twitter {
    users: HashMap<i32, User>,     // 用户ID -> 用户信息
    tweet_time: HashMap<i32, i32>, // 推文ID -> 发布时间戳
    timestamp: i32,                // 全局时间戳
}

impl Twitter {
    /// 初始化推特对象
    pub fn new() -> Self {
        Twitter {
            users: HashMap::new(),
            tweet_time: HashMap::new(),
            timestamp: 0,
        }
    }

    /// 添加新用户（如果不存在）
    fn add_user(&mut self, user_id: i32) {
        self.users.entry(user_id).or_insert_with(User::new);
    }

    /// 发布推文
    pub fn post_tweet(&mut self, user_id: i32, tweet_id: i32) {
        // 确保用户存在
        self.add_user(user_id);

        // 记录推文时间戳
        self.timestamp += 1;
        self.tweet_time.insert(tweet_id, self.timestamp);

        // 用户发布推文
        if let Some(user) = self.users.get_mut(&user_id) {
            user.post_tweet(tweet_id);
        }
    }

    /// 获取新闻推送（最近 10 条推文）
    pub fn get_news_feed(&self, user_id: i32) -> Vec<i32> {
        // 如果用户不存在，返回空
        if !self.users.contains_key(&user_id) {
            return Vec::new();
        }

        let mut all_tweets = Vec::new();

        // 获取用户自己的推文
        if let Some(user) = self.users.get(&user_id) {
            all_tweets.extend(user.tweets.iter().cloned());
        }

        // 获取关注对象的推文
        // 注意：需要遍历所有用户，检查谁关注了当前用户
        for (followee_id, followee) in self.users.iter() {
            if followee.followers.contains(&user_id) && *followee_id != user_id {
                all_tweets.extend(followee.tweets.iter().cloned());
            }
        }

        // 按时间戳倒序排序（最新的在前）
        all_tweets.sort_by_key(|tweet_id| Reverse(self.tweet_time.get(tweet_id).unwrap_or(&0)));

        // 只返回最近 10 条
        all_tweets.into_iter().take(10).collect()
    }

    /// 关注用户
    pub fn follow(&mut self, follower_id: i32, followee_id: i32) {
        // 不能关注自己
        if follower_id == followee_id {
            return;
        }

        // 确保两个用户都存在
        self.add_user(follower_id);
        self.add_user(followee_id);

        // 添加关注关系
        if let Some(user) = self.users.get_mut(&followee_id) {
            user.add_follower(follower_id);
        }
    }

    /// 取消关注用户
    pub fn unfollow(&mut self, follower_id: i32, followee_id: i32) {
        // 不能取消关注自己
        if follower_id == followee_id {
            return;
        }

        // 如果被关注用户不存在，忽略操作
        if !self.users.contains_key(&followee_id) {
            return;
        }

        // 移除关注关系
        if let Some(user) = self.users.get_mut(&followee_id) {
            user.remove_follower(follower_id);
        }
    }
}
```
