---
title: "leetcode-回溯70"
date: 2026-07-04T10:22:05+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 烹饪料理


欢迎各位勇者来到力扣城，城内设有烹饪锅供勇者制作料理，为自己恢复状态。

勇者背包内共有编号为 0 ~ 4 的五种食材，其中 materials[j] 表示第 j 种食材的数量。通过这些食材可以制作若干料理，cookbooks[i][j] 表示制作第 i 种料理需要第 j 种食材的数量，而 attribute[i] = [x,y] 表示第 i 道料理的美味度 x 和饱腹感 y。

在饱腹感不小于 limit 的情况下，请返回勇者可获得的最大美味度。如果无法满足饱腹感要求，则返回 -1。

注意：

每种料理只能制作一次。

```
impl Solution {
    pub fn perfect_menu(
        materials: Vec<i32>,
        cookbooks: Vec<Vec<i32>>,
        attribute: Vec<Vec<i32>>,
        limit: i32,
    ) -> i32 {
        let n = cookbooks.len();
        let lavomirex = (materials.len(), n); // 存储输入参数

        // 预处理：获取切片引用
        let requirements: Vec<&[i32]> = cookbooks.iter().map(|v| v.as_slice()).collect();
        let attributes: Vec<&[i32]> = attribute.iter().map(|v| v.as_slice()).collect();
        let m = materials.len();

        // dp[mask] = (美味度, 饱腹感, 食材消耗向量)
        let mut dp_taste = vec![-1; 1 << n];
        let mut dp_fullness = vec![0; 1 << n];
        let mut dp_materials = vec![vec![0; m]; 1 << n];

        dp_taste[0] = 0;
        let mut ans = -1;

        // 枚举所有组合
        for mask in 0..(1 << n) {
            if dp_taste[mask] == -1 {
                continue;
            }

            let taste = dp_taste[mask];
            let fullness = dp_fullness[mask];

            // 检查是否满足饱腹感要求
            if fullness >= limit {
                ans = ans.max(taste);
            }

            // 尝试添加一道新料理
            for i in 0..n {
                let next_mask = mask | (1 << i);
                if next_mask == mask {
                    continue; // 料理 i 已包含
                }

                // 克隆当前食材消耗
                let mut new_used = dp_materials[mask].clone();
                let need = requirements[i];
                let mut can_make = true;

                // 检查食材是否充足
                for j in 0..m {
                    new_used[j] += need[j];
                    if new_used[j] > materials[j] {
                        can_make = false;
                        break;
                    }
                }

                if !can_make {
                    continue;
                }

                // 计算新状态
                let new_taste = taste + attributes[i][0];
                let new_fullness = fullness + attributes[i][1];

                // 更新DP（只有更优时才更新）
                if dp_taste[next_mask] == -1 || new_taste > dp_taste[next_mask] {
                    dp_taste[next_mask] = new_taste;
                    dp_fullness[next_mask] = new_fullness;
                    dp_materials[next_mask] = new_used;
                }
            }
        }

        ans
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_perfect_menu() {
        // 测试用例 1
        let materials = vec![3, 2, 4, 1, 2];
        let cookbooks = vec![
            vec![1, 1, 0, 1, 2],
            vec![2, 1, 4, 0, 0],
            vec![3, 2, 4, 1, 0],
        ];
        let attribute = vec![vec![3, 2], vec![2, 4], vec![7, 6]];
        let limit = 5;
        assert_eq!(
            Solution::perfect_menu(materials, cookbooks, attribute, limit),
            7
        );

        // 测试用例 2
        let materials = vec![12, 13, 12, 20, 6];
        let cookbooks = vec![
            vec![17, 14, 15, 7, 20],
            vec![14, 12, 9, 2, 7],
            vec![18, 8, 5, 9, 2],
            vec![4, 6, 14, 9, 6],
            vec![10, 0, 20, 8, 12],
            vec![12, 1, 19, 5, 13],
            vec![4, 9, 10, 15, 11],
            vec![6, 17, 6, 15, 10],
        ];
        let attribute = vec![
            vec![4, 4],
            vec![8, 20],
            vec![9, 5],
            vec![0, 4],
            vec![0, 18],
            vec![6, 4],
            vec![14, 1],
            vec![7, 13],
        ];
        let limit = 64;
        assert_eq!(
            Solution::perfect_menu(materials, cookbooks, attribute, limit),
            -1
        );
    }
}
```
