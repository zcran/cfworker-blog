---
title: "leetcode-树状树组13"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 以组为单位订音乐会的门票

一个音乐会总共有 n 排座位，编号从 0 到 n - 1 ，每一排有 m 个座椅，编号为 0 到 m - 1 。你需要设计一个买票系统，针对以下情况进行座位安排：

  ·同一组的 k 位观众坐在 同一排座位，且座位连续 。

  ·k 位观众中 每一位 都有座位坐，但他们 不一定 坐在一起。

由于观众非常挑剔，所以：

  ·只有当一个组里所有成员座位的排数都 小于等于 maxRow ，这个组才能订座位。每一组的 maxRow 可能 不同 。

  ·如果有多排座位可以选择，优先选择 最小 的排数。如果同一排中有多个座位可以坐，优先选择号码 最小 的。

请你实现 BookMyShow 类：

  ·BookMyShow(int n, int m) ，初始化对象，n 是排数，m 是每一排的座位数。

  ·int[] gather(int k, int maxRow) 返回长度为 2 的数组，表示 k 个成员中 第一个座位 的排数和座位编号，这 k 位成员必须坐在 同一排座位，且座位连续 。换言之，返回最小可能的 r 和 c 满足第 r 排中 [c, c + k - 1] 的座位都是空的，且 r <= maxRow 。如果 无法 安排座位，返回 [] 。

  ·boolean scatter(int k, int maxRow) 如果组里所有 k 个成员 不一定 要坐在一起的前提下，都能在第 0 排到第 maxRow 排之间找到座位，那么请返回 true 。这种情况下，每个成员都优先找排数 最小 ，然后是座位编号最小的座位。如果不能安排所有 k 个成员的座位，请返回 false 。


```
/// 电影院订票系统，支持两种操作：
/// - `gather(k, max_row)`：在 `[0, max_row]` 行中寻找第一个剩余座位 ≥ k 的行，并占用该行的 k 个座位。
/// - `scatter(k, max_row)`：在 `[0, max_row]` 行中分散占用 k 个座位（从第一个未满的行开始依次填充），成功返回 true。
///
/// 内部使用线段树维护每行的已占用座位数，支持：
/// - 单点更新 (`update`)
/// - 区间和查询 (`query_sum`)
/// - 区间内寻找第一个已占用座位 ≤ 特定阈值的行 (`find_first`)。
struct BookMyShow {
    rows: usize,          // 总行数
    seats_per_row: i32,   // 每行的座位总数
    min_occupied: Vec<i32>, // 线段树结点：区间内已占用座位的最小值
    sum_occupied: Vec<i64>, // 线段树结点：区间内已占用座位的总和
}

impl BookMyShow {
    /// 创建新实例。
    /// `n` 为行数，`m` 为每行座位数。
    pub fn new(n: i32, m: i32) -> Self {
        let n = n as usize;
        // 线段树大小：比 n 大的最小 2 的幂的两倍（安全且节省空间）
        let size = n.next_power_of_two() * 2;
        BookMyShow {
            rows: n,
            seats_per_row: m,
            min_occupied: vec![0; size],
            sum_occupied: vec![0; size],
        }
    }

    // ==================== 线段树核心操作 ====================

    /// 将第 `idx` 行的已占用座位数增加 `delta`。
    fn update(&mut self, node: usize, l: usize, r: usize, idx: usize, delta: i32) {
        if l == r {
            self.min_occupied[node] += delta;
            self.sum_occupied[node] += delta as i64;
            return;
        }
        let mid = (l + r) / 2;
        if idx <= mid {
            self.update(node * 2, l, mid, idx, delta);
        } else {
            self.update(node * 2 + 1, mid + 1, r, idx, delta);
        }
        // 合并左右子树信息
        self.min_occupied[node] = self.min_occupied[node * 2].min(self.min_occupied[node * 2 + 1]);
        self.sum_occupied[node] = self.sum_occupied[node * 2] + self.sum_occupied[node * 2 + 1];
    }

    /// 查询区间 `[L, R]` 内的已占用座位总和。
    fn query_sum(&self, node: usize, l: usize, r: usize, L: usize, R: usize) -> i64 {
        if L <= l && r <= R {
            return self.sum_occupied[node];
        }
        let mid = (l + r) / 2;
        let mut res = 0;
        if L <= mid {
            res += self.query_sum(node * 2, l, mid, L, R);
        }
        if R > mid {
            res += self.query_sum(node * 2 + 1, mid + 1, r, L, R);
        }
        res
    }

    /// 在区间 `[0, R]` 内寻找第一个满足 `已占用座位数 <= threshold` 的行。
    /// 若找到返回其索引，否则返回 `None`。
    fn find_first(&self, node: usize, l: usize, r: usize, R: usize, threshold: i32) -> Option<usize> {
        // 当前区间与查询区间无交集 或 最小值已超过阈值
        if l > R || self.min_occupied[node] > threshold {
            return None;
        }
        if l == r {
            return Some(l);
        }
        let mid = (l + r) / 2;
        // 优先在左子树中寻找（保证最左）
        if let Some(idx) = self.find_first(node * 2, l, mid, R, threshold) {
            return Some(idx);
        }
        // 左子树无解，尝试右子树（前提是右子树区间与 [0,R] 有重叠）
        if mid < R {
            self.find_first(node * 2 + 1, mid + 1, r, R, threshold)
        } else {
            None
        }
    }

    // ==================== 辅助方法 ====================

    /// 获取第 `row` 行的当前已占用座位数。
    fn occupied_in_row(&self, row: usize) -> i32 {
        self.query_sum(1, 0, self.rows - 1, row, row) as i32
    }

    /// 获取 `[0, max_row]` 所有行的已占用座位总和。
    fn total_occupied_up_to(&self, max_row: usize) -> i64 {
        self.query_sum(1, 0, self.rows - 1, 0, max_row)
    }

    // ==================== 对外接口 ====================

    /// 在 `[0, max_row]` 行中寻找第一个剩余座位 ≥ k 的行，并占用该行的 k 个座位。
    /// 返回 `[row, col]`，其中 `col` 是该行被占用的起始列号（等于原已占用座位数）。
    /// 如果找不到这样的行，返回空数组。
    pub fn gather(&mut self, k: i32, max_row: i32) -> Vec<i32> {
        let max_row = max_row as usize;
        // 需要行的已占用座位 ≤ seats_per_row - k，即剩余容量 ≥ k
        let threshold = self.seats_per_row - k;
        let row = match self.find_first(1, 0, self.rows - 1, max_row, threshold) {
            Some(r) => r,
            None => return vec![],
        };
        let col = self.occupied_in_row(row);
        self.update(1, 0, self.rows - 1, row, k);
        vec![row as i32, col]
    }

    /// 在 `[0, max_row]` 行中分散占用 k 个座位。
    /// 从第一个未满的行开始，依次填充，直到 k 个座位全部分配完毕。
    /// 返回是否成功（即总剩余座位足够）。
    pub fn scatter(&mut self, mut k: i32, max_row: i32) -> bool {
        let max_row = max_row as usize;
        let total_capacity = self.seats_per_row as i64 * (max_row + 1) as i64;
        let already_occupied = self.total_occupied_up_to(max_row);
        // 检查总剩余容量是否足够
        if total_capacity - already_occupied < k as i64 {
            return false;
        }

        // 从第一个未满的行开始：未满表示已占用座位数 < seats_per_row
        let mut row = match self.find_first(1, 0, self.rows - 1, max_row, self.seats_per_row - 1) {
            Some(r) => r,
            None => return true, // 所有行都满了，但总容量检查已通过，不可能发生
        };

        while k > 0 {
            let used = self.occupied_in_row(row);
            let available = self.seats_per_row - used;
            let take = k.min(available);
            self.update(1, 0, self.rows - 1, row, take);
            k -= take;
            row += 1; // 转向下一行
        }
        true
    }
}
```
