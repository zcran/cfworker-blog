---
title: "leetcode-图11"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 获取你好友已观看的视频

有 n 个人，每个人都有一个  0 到 n-1 的唯一 id 。

给你数组 watchedVideos  和 friends ，其中 watchedVideos[i]  和 friends[i] 分别表示 id = i 的人观看过的视频列表和他的好友列表。

Level 1 的视频包含所有你好友观看过的视频，level 2 的视频包含所有你好友的好友观看过的视频，以此类推。一般的，Level 为 k 的视频包含所有从你出发，最短距离为 k 的好友观看过的视频。

给定你的 id  和一个 level 值，请你找出所有指定 level 的视频，并将它们按观看频率升序返回。如果有频率相同的视频，请将它们按字母顺序从小到大排列。


```
impl Solution {
    pub fn watched_videos_by_friends(
        watched_videos: Vec<Vec<String>>,
        friends: Vec<Vec<i32>>,
        id: i32,
        level: i32,
    ) -> Vec<String> {
        use std::collections::{VecDeque, HashMap};
        let n = friends.len();
        let id = id as usize;
        let level = level as usize;

        // BFS 找到距离为 level 的所有朋友
        let mut queue = VecDeque::new();
        let mut visited = vec![false; n];
        let mut dist = vec![0; n];

        queue.push_back(id);
        visited[id] = true;

        while let Some(cur) = queue.pop_front() {
            if dist[cur] == level {
                continue; // 不再扩展更远的朋友
            }
            for &next in &friends[cur] {
                let next = next as usize;
                if !visited[next] {
                    visited[next] = true;
                    dist[next] = dist[cur] + 1;
                    queue.push_back(next);
                }
            }
        }

        // 统计 level 层朋友观看的视频频率
        let mut freq = HashMap::new();
        for i in 0..n {
            if dist[i] == level {
                for video in &watched_videos[i] {
                    *freq.entry(video).or_insert(0) += 1;
                }
            }
        }

        // 按频率升序，频率相同按视频名字典序排序
        let mut ans: Vec<_> = freq.into_iter().collect();
        ans.sort_by(|a, b| a.1.cmp(&b.1).then(a.0.cmp(b.0)));
        ans.into_iter().map(|(video, _)| video.clone()).collect()
    }
}
```
