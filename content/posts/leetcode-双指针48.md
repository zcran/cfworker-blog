---
title: "leetcode-双指针48"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 适龄的朋友

在社交媒体网站上有 n 个用户。给你一个整数数组 ages ，其中 ages[i] 是第 i 个用户的年龄。

如果下述任意一个条件为真，那么用户 x 将不会向用户 y（x != y）发送好友请求：

ages[y] <= 0.5 * ages[x] + 7
ages[y] > ages[x]
ages[y] > 100 && ages[x] < 100

否则，x 将会向 y 发送一条好友请求。

注意，如果 x 向 y 发送一条好友请求，y 不必也向 x 发送一条好友请求。另外，用户不会向自己发送好友请求。

返回在该社交媒体网站上产生的好友请求总数。


```
impl Solution {
    pub fn num_friend_requests(ages: Vec<i32>) -> i32 {
        // 年龄范围只有 1..120
        let mut count = [0; 121];
        for &age in &ages {
            count[age as usize] += 1;
        }

        // 前缀和，用于快速查询区间内的人数
        let mut prefix = [0; 121];
        for i in 1..=120 {
            prefix[i] = prefix[i - 1] + count[i];
        }

        let mut total = 0;
        for age_x in 1..=120 {
            let cnt_x = count[age_x];
            if cnt_x == 0 {
                continue;
            }

            // 满足 ages[y] > 0.5 * age_x + 7 的最小年龄
            // 等价于 2 * ages[y] > age_x + 14，即 ages[y] >= (age_x + 14) / 2 + 1
            let min_age_y = (age_x + 14) / 2 + 1;
            if min_age_y > age_x {
                continue;
            }

            // 年龄在 [min_age_y, age_x] 范围内的人数
            let in_range = prefix[age_x] - prefix[min_age_y - 1];
            // 每个人不能给自己发请求，所以减去自己（1 个）
            total += cnt_x * (in_range - 1);
        }

        total
    }
}
```
