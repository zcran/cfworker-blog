---
title: "leetcode-数学15"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 大样本统计

我们对 0 到 255 之间的整数进行采样，并将结果存储在数组 count 中：count[k] 就是整数 k 在样本中出现的次数。

计算以下统计数据:

minimum ：样本中的最小元素。
maximum ：样品中的最大元素。
mean ：样本的平均值，计算为所有元素的总和除以元素总数。
median ：
    如果样本的元素个数是奇数，那么一旦样本排序后，中位数 median 就是中间的元素。
      如果样本中有偶数个元素，那么中位数median 就是样本排序后中间两个元素的平均值。
mode ：样本中出现次数最多的数字。保众数是 唯一 的。

以浮点数数组的形式返回样本的统计信息 [minimum, maximum, mean, median, mode] 。与真实答案误差在 10-5 内的答案都可以通过。


```
impl Solution {
    /// 根据 0..=255 的频次数组 count，返回统计信息：
    /// [minimum, maximum, mean, median, mode]
    ///
    /// 一趟遍历同时求出最值、众数、样本总数与平均值；
    /// 再用一趟遍历通过累计频次定位两个中位数下标，避免额外存储。
    pub fn sample_stats(count: Vec<i32>) -> Vec<f64> {
        let mut min_val = 255i32;
        let mut max_val = 0i32;
        let mut total_cnt: i32 = 0;
        let mut average = 0.0f64;
        let (mut mode, mut mode_cnt) = (0i32, 0i32);

        // 第一趟：最值、众数、总数
        for (x, &cnt) in count.iter().enumerate() {
            if cnt == 0 {
                continue;
            }
            let x = x as i32;
            min_val = min_val.min(x);
            max_val = max_val.max(x);
            total_cnt += cnt;
            if cnt > mode_cnt {
                mode_cnt = cnt;
                mode = x;
            }
        }

        // 两个中位数在排序后序列中的下标（0-based）
        // 奇数个：两下标相同；偶数个：分别为中间偏左与偏右
        let (mut idx1, mut idx2) = if total_cnt & 1 == 1 {
            let m = total_cnt >> 1;
            (m, m)
        } else {
            let m = total_cnt >> 1;
            (m - 1, m)
        };

        let (mut mid1, mut mid2) = (0i32, 0i32);

        // 第二趟：平均值 + 中位数定位
        for (x, cnt) in count.into_iter().enumerate() {
            if cnt == 0 {
                continue;
            }
            average += x as f64 * cnt as f64 / total_cnt as f64;

            // 减去当前组的频次后若下标变负，说明目标落在该组
            if idx1 >= 0 {
                idx1 -= cnt;
                if idx1 < 0 {
                    mid1 = x as i32;
                }
            }
            if idx2 >= 0 {
                idx2 -= cnt;
                if idx2 < 0 {
                    mid2 = x as i32;
                }
            }
        }

        vec![
            min_val as f64,
            max_val as f64,
            average,
            (mid1 + mid2) as f64 / 2.0,
            mode as f64,
        ]
    }
}
```
