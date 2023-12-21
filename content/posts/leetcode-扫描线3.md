---
title: "Leetcode 扫描线3"
date: 2023-12-19T15:44:18+08:00
tags: ["leetcode", "扫描线"]
draft: false
---

## 天际线问题

城市的 天际线 是从远处观看该城市中所有建筑物形成的轮廓的外部轮廓。给你所有建筑物的位置和高度，请返回 由这些建筑物形成的 天际线 。

每个建筑物的几何信息由数组 buildings 表示，其中三元组 buildings[i] = [lefti, righti, heighti] 表示：

left⌄i 是第 i 座建筑物左边缘的 x 坐标。
right⌄i 是第 i 座建筑物右边缘的 x 坐标。
height⌄i 是第 i 座建筑物的高度。
你可以假设所有的建筑都是完美的长方形，在高度为 0 的绝对平坦的表面上。

天际线 应该表示为由 “关键点” 组成的列表，格式 [[x⌄1,y⌄1],[x⌄2,y⌄2],...] ，并按 x 坐标 进行 排序 。关键点是水平线段的左端点。列表中最后一个点是最右侧建筑物的终点，y 坐标始终为 0 ，仅用于标记天际线的终点。此外，任何两个相邻建筑物之间的地面都应被视为天际线轮廓的一部分。

注意：输出天际线中不得有连续的相同高度的水平线。例如 [...[2 3], [4 5], [7 5], [11 5], [12 7]...] 是不正确的答案；三条高度为 5 的线应该在最终输出中合并为一个：[...[2 3], [4 5], [12 7], ...]

```
impl Solution {
    pub fn get_skyline(buildings: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let mut boundaries:Vec<i32> = buildings.iter().map(|x|x[..2].to_vec()).flatten().collect();
        boundaries.sort();
        let mut q = std::collections::BinaryHeap::new();
        let mut idx = 0;
        let n = buildings.len();
        let mut ans = Vec::<Vec<i32>>::new();
        for &boundary in &boundaries{
            while idx < n && buildings[idx][0] <= boundary{
                q.push(vec![buildings[idx][2], buildings[idx][1]]);
                idx += 1;
            }
            while !q.is_empty() && q.peek().unwrap()[1] <= boundary{
                q.pop();
            }
            let mut maxn = if let Some(v) = q.peek(){
                v[0]
            } else { 0 };
            if ans.is_empty() || maxn != ans[ans.len() - 1][1]{
                ans.push(vec![boundary, maxn]);
            }
        }
        ans
    }
}
```