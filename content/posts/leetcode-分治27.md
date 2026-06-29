---
title: "leetcode-分治27"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 查询超过阈值频率最高元素


给你一个长度为 n 的整数数组 nums 和一个查询数组 queries，其中 queries[i] = [li, ri, thresholdi]。

返回一个整数数组 ans，其中 ans[i] 等于子数组 nums[li...ri] 中出现 至少 thresholdi 次的元素，选择频率 最高 的元素（如果频率相同则选择 最小 的元素），如果不存在这样的元素则返回 -1。



```
use std::collections::HashMap;

impl Solution {
    /// 查询子数组中出现至少 threshold 次的众数
    ///
    /// # 参数
    /// - `nums`: 原始整数数组
    /// - `queries`: 查询数组，每个查询为 [l, r, threshold]（闭区间）
    ///
    /// # 返回
    /// 每个查询的结果：满足条件的众数（频数最高，相同时取最小），不存在则返回 -1
    ///
    /// # 算法
    /// 使用块状划分 + 莫队思想优化查询：
    /// - 小区间（长度 ≤ block_size）直接暴力统计
    /// - 大区间离线处理，利用块内有序性减少重复计算
    pub fn subarray_majority(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = nums.len();
        let m = queries.len();

        // 块大小：平衡区间移动和暴力查询的复杂度
        // 理论最优 ≈ n / sqrt(2m)，这里简化取整
        let block_size = (n as f64 / (2.0 * m as f64).sqrt()).ceil() as usize;
        let block_size = block_size.max(1); // 至少为 1

        // 用于统计频数的结构
        struct Counter {
            freq: HashMap<i32, i32>,
            max_cnt: i32,
            min_val: i32,
        }

        impl Counter {
            fn new() -> Self {
                Self {
                    freq: HashMap::new(),
                    max_cnt: 0,
                    min_val: 0,
                }
            }

            /// 添加元素并更新众数信息
            fn add(&mut self, x: i32) {
                let cnt = self.freq.entry(x).or_insert(0);
                *cnt += 1;

                match (*cnt).cmp(&self.max_cnt) {
                    std::cmp::Ordering::Greater => {
                        self.max_cnt = *cnt;
                        self.min_val = x;
                    }
                    std::cmp::Ordering::Equal if x < self.min_val => {
                        self.min_val = x;
                    }
                    _ => {}
                }
            }

            /// 删除元素（用于回滚）
            fn remove(&mut self, x: i32) {
                if let Some(cnt) = self.freq.get_mut(&x) {
                    *cnt -= 1;
                    // 注意：这里不更新 max_cnt/min_val，因为只在回滚时使用
                    // 回滚后会用快照恢复，无需维护精确值
                }
            }

            /// 获取当前众数（如果达到阈值）
            fn get_result(&self, threshold: i32) -> i32 {
                if self.max_cnt >= threshold {
                    self.min_val
                } else {
                    -1
                }
            }

            /// 保存当前状态快照
            fn snapshot(&self) -> (i32, i32) {
                (self.max_cnt, self.min_val)
            }

            /// 恢复状态快照
            fn restore(&mut self, max_cnt: i32, min_val: i32) {
                self.max_cnt = max_cnt;
                self.min_val = min_val;
            }

            /// 清空所有数据
            fn clear(&mut self) {
                self.freq.clear();
                self.max_cnt = 0;
                self.min_val = 0;
            }
        }

        // 离线查询结构
        #[derive(Clone, Copy)]
        struct Query {
            block_id: usize,    // 所属块编号
            l: usize,           // 左边界（包含）
            r: usize,           // 右边界（不包含）
            threshold: i32,
            idx: usize,         // 原始查询索引
        }

        let mut counter = Counter::new();
        let mut ans = vec![-1; m];
        let mut big_queries = Vec::new();

        // 分类处理查询：小区间暴力，大区间离线
        for (idx, q) in queries.iter().enumerate() {
            let l = q[0] as usize;
            let r = q[1] as usize + 1; // 转换为左闭右开区间
            let threshold = q[2];

            // 小区间：长度小于等于块大小，直接暴力
            if r - l <= block_size {
                for j in l..r {
                    counter.add(nums[j]);
                }
                if counter.max_cnt >= threshold {
                    ans[idx] = counter.min_val;
                }
                counter.clear();
            }
            // 大区间：加入离线处理队列
            else {
                big_queries.push(Query {
                    block_id: l / block_size,
                    l,
                    r,
                    threshold,
                    idx,
                });
            }
        }

        // 按 (块ID, 右边界) 排序，优化右指针移动
        big_queries.sort_by(|a, b| {
            a.block_id.cmp(&b.block_id).then(a.r.cmp(&b.r))
        });

        let mut current_r = 0;
        let mut last_block = usize::MAX;

        for q in big_queries {
            // 进入新块时，重置状态并移动右指针到块右边界
            if q.block_id != last_block {
                let block_start = (q.block_id + 1) * block_size;
                current_r = block_start;
                counter.clear();
                last_block = q.block_id;
            }

            // 移动右指针到查询右边界
            while current_r < q.r {
                counter.add(nums[current_r]);
                current_r += 1;
            }

            // 保存当前状态（右半部分已统计完成）
            let (saved_max_cnt, saved_min_val) = counter.snapshot();

            // 统计左半部分（从查询左边界到块右边界）
            let block_start = (q.block_id + 1) * block_size;
            for j in q.l..block_start {
                counter.add(nums[j]);
            }

            // 获取查询结果
            if counter.max_cnt >= q.threshold {
                ans[q.idx] = counter.min_val;
            }

            // 回滚左半部分的统计
            counter.restore(saved_max_cnt, saved_min_val);
            for j in q.l..block_start {
                counter.remove(nums[j]);
            }
        }

        ans
    }
}
```
