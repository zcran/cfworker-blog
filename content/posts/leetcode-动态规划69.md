---
title: "leetcode-动态规划69"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 删除一次得到子数组最大和

给你一个整数数组，返回它的某个 非空 子数组（连续元素）在执行一次可选的删除操作后，所能得到的最大元素总和。换句话说，你可以从原数组中选出一个子数组，并可以决定要不要从中删除一个元素（只能删一次哦），（删除后）子数组中至少应当有一个元素，然后该子数组（剩下）的元素总和是所有子数组之中最大的。

注意，删除一个元素后，子数组 不能为空。


```
impl Solution {
    /// 1186. 删除一次得到子数组最大和
    ///
    /// 定义两个状态，一次遍历即可：
    ///   keep：以当前元素结尾、未删除过元素的子数组最大和
    ///   del ：以当前元素结尾、已删除过一个元素的子数组最大和
    ///
    /// 对每个 x：
    ///   new_keep = max(keep + x, x)           // 要么接上，要么从 x 重新开始
    ///   new_del  = max(del + x, keep)         // 要么延续已删状态，要么在 x 处删掉 x（即不取 x）
    ///   ans = max(ans, new_keep, new_del)
    ///
    /// 注意 del 的初始值为 i32::MIN / 2（表示不可达），保证第一个元素时不会误删。
    /// 最终答案至少是一个元素（keep 的初值处理）。
    pub fn maximum_sum(arr: Vec<i32>) -> i32 {
        const NEG: i32 = i32::MIN / 2;
        let mut keep = NEG; // 未删除
        let mut del = NEG;  // 已删除一次
        let mut ans = NEG;

        for &x in &arr {
            // 先更新 del，需用旧的 keep
            let new_del = (del + x).max(keep);
            let new_keep = (keep + x).max(x);
            keep = new_keep;
            del = new_del;
            ans = ans.max(keep).max(del);
        }

        ans
    }
}
```
