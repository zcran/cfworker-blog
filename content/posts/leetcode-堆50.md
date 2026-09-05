---
title: "leetcode-堆50"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最流行的视频创作者

给你两个字符串数组 creators 和 ids ，和一个整数数组 views ，所有数组的长度都是 n 。平台上第 i 个视频者是 creator[i] ，视频分配的 id 是 ids[i] ，且播放量为 views[i] 。

视频创作者的 流行度 是该创作者的 所有 视频的播放量的 总和 。请找出流行度 最高 创作者以及该创作者播放量 最大 的视频的 id 。

如果存在多个创作者流行度都最高，则需要找出所有符合条件的创作者。

如果某个创作者存在多个播放量最高的视频，则只需要找出字典序最小的 id 。

返回一个二维字符串数组 answer ，其中 answer[i] = [creatorˇi, idˇi] 表示 creatorˇi 的流行度 最高 且其最流行的视频 id 是 idˇi ，可以按任何顺序返回该结果。


```
use std::collections::HashMap;

impl Solution {
    /// 找出最受欢迎的视频创作者。
    ///
    /// 规则：
    /// - "最受欢迎" 定义为该创作者所有视频的**总播放量**最高（可能有多个并列）。
    /// - 对每个创作者，返回其**播放量最高**的视频 id；若播放量相同，取字典序最小的 id。
    pub fn most_popular_creator(
        creators: Vec<String>,
        ids: Vec<String>,
        views: Vec<i32>,
    ) -> Vec<Vec<String>> {
        // 结构说明：
        // key   -> 创作者名字
        // value -> ((最佳视频 id, 最佳视频播放量), 该创作者总播放量)
        let mut creator_map: HashMap<String, ((String, i32), i32)> = HashMap::new();
        let mut max_total_views = i32::MIN;

        // 同时遍历三个数组：创作者、视频 id、播放量
        for ((creator, mut video_id), view) in creators
            .into_iter()
            .zip(ids.into_iter())
            .zip(views.into_iter())
        {
            match creator_map.entry(creator) {
                std::collections::hash_map::Entry::Occupied(mut entry) => {
                    let ((best_id, best_view), total_views) = entry.get_mut();

                    // 累加该创作者的总播放量
                    *total_views += view;
                    max_total_views = max_total_views.max(*total_views);

                    // 更新该创作者播放量最高的视频
                    if view > *best_view {
                        // 严格更高，直接替换
                        (*best_view, *best_id) = (view, video_id);
                    } else if view == *best_view {
                        // 播放量相同，取字典序更小的 id
                        if best_id > &mut video_id {
                            *best_id = video_id;
                        }
                    }
                }
                std::collections::hash_map::Entry::Vacant(entry) => {
                    // 首次遇到该创作者：
                    // 当前视频即为其最佳视频，总播放量初始化为当前视频播放量
                    entry.insert(((video_id, view), view));
                    max_total_views = max_total_views.max(view);
                }
            }
        }

        // 筛选出总播放量等于最大值的创作者，输出 [创作者, 最佳视频 id]
        let mut result: Vec<Vec<String>> = creator_map
            .into_iter()
            .filter(|(_, (_, total_views))| *total_views == max_total_views)
            .map(|(creator, ((best_id, _), _))| vec![creator, best_id])
            .collect();

        // 防御性处理：理论上不会为空，但保留原逻辑
        if result.is_empty() {
            result.push(vec!["a".to_string(), "a".to_string()]);
        }

        result
    }
}
```
