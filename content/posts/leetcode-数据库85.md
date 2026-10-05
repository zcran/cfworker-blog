---
title: "leetcode-数据库85"
date: 2026-09-15T10:27:55+08:00
tags: ["leetcode", "数据库"]
draft: false
---


## 分析订阅转化

表：UserActivity

+------------------+---------+
| Column Name      | Type    |
+------------------+---------+
| user_id          | int     |
| activity_date    | date    |
| activity_type    | varchar |
| activity_duration| int     |
+------------------+---------+
(user_id, activity_date, activity_type) 是这张表的唯一主键。
activity_type 是('free_trial', 'paid', 'cancelled')中的一个。
activity_duration 是用户当天在平台上花费的分钟数。
每一行表示一个用户在特定日期的活动。
订阅服务想要分析用户行为模式。公司提供7天免费试用，试用结束后，用户可以选择订阅 付费计划 或 取消。编写解决方案：

1. 查找从免费试用转为付费订阅的用户
2. 计算每位用户在 免费试用 期间的 平均每日活动时长（四舍五入至小数点后 2 位）
3. 计算每位用户在 付费 订阅期间的 平均每日活动时长（四舍五入到小数点后 2 位）

返回结果表以 user_id 升序 排序。


```
-- PostgreSQL 解决方案
-- 目标：找出从免费试用转为付费订阅的用户，并分别计算
--   试用期间的平均每日活动时长 trial_avg_duration
--   付费期间的平均每日活动时长 paid_avg_duration
-- 判定“试用转付费”：该用户既有 free_trial 记录，也有 paid 记录
-- 两者均四舍五入到两位小数，结果按 user_id 升序

SELECT
    user_id,
    -- 只对 free_trial 行求平均，其余为 NULL 自动忽略
    ROUND(AVG(activity_duration) FILTER (WHERE activity_type = 'free_trial'), 2) AS trial_avg_duration,
    -- 只对 paid 行求平均
    ROUND(AVG(activity_duration) FILTER (WHERE activity_type = 'paid'), 2)       AS paid_avg_duration
FROM UserActivity
GROUP BY user_id
-- 必须既有免费试用记录，又有付费记录（排除仅有付费、或试用后取消的用户）
HAVING COUNT(*) FILTER (WHERE activity_type = 'free_trial') >= 1
   AND COUNT(*) FILTER (WHERE activity_type = 'paid')       >= 1
ORDER BY user_id ASC;
```
