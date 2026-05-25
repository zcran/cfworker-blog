---
title: "leetcode-队列19"
date: 2026-05-23T09:21:38+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 魔术排列

秋日市集上，魔术师邀请小扣与他互动。魔术师的道具为分别写有数字 1~N 的 N 张卡牌，然后请小扣思考一个 N 张卡牌的排列 target。

魔术师的目标是找到一个数字 k（k >= 1），使得初始排列顺序为 1~N 的卡牌经过特殊的洗牌方式最终变成小扣所想的排列 target，特殊的洗牌方式为：

· 第一步，魔术师将当前位于 偶数位置 的卡牌（下标自 1 开始），保持 当前排列顺序 放在位于 奇数位置 的卡牌之前。例如：将当前排列 [1,2,3,4,5] 位于偶数位置的 [2,4] 置于奇数位置的 [1,3,5] 前，排列变为 [2,4,1,3,5]；

· 第二步，若当前卡牌数量小于等于 k，则魔术师按排列顺序取走全部卡牌；若当前卡牌数量大于 k，则取走前 k 张卡牌，剩余卡牌继续重复这两个步骤，直至所有卡牌全部被取走；

卡牌按照魔术师取走顺序构成的新排列为「魔术取数排列」，请返回是否存在这个数字 k 使得「魔术取数排列」恰好就是 target，从而让小扣感到大吃一惊。


```
impl Solution {
    /// 判断是否存在 k 使得洗牌取数过程得到 target
    pub fn is_magic(target: Vec<i32>) -> bool {
        let n = target.len();
        if n == 1 {
            return true;
        }

        // 初始排列 1..n
        let mut nums: Vec<i32> = (1..=n as i32).collect();

        // 进行一次洗牌，得到第一次洗牌后的排列
        Self::magic_shuffle(&mut nums);

        // k = 第一次洗牌后与 target 的最长公共前缀长度
        let k = Self::common_prefix_len(&nums, &target);
        if k == 0 {
            return false;
        }

        let mut cur = nums;       // 当前排列（已经洗牌，未取走）
        let mut pos = 0;          // target 中已匹配的位置

        while !cur.is_empty() {
            let take = k.min(cur.len());          // 本轮应取数量（不足 k 则全取）
            // 检查取出的部分是否与 target 对应段一致
            for i in 0..take {
                if cur[i] != target[pos + i] {
                    return false;
                }
            }
            pos += take;
            if pos == n {
                return true;                      // 完全匹配
            }
            // 移除已取走的部分
            if take < cur.len() {
                cur.drain(0..take);
            } else {
                cur.clear();
                break;
            }
            // 对剩余部分进行下一轮洗牌
            Self::magic_shuffle(&mut cur);
        }

        pos == n
    }

    /// 洗牌：偶数位置（1-based）的元素放在奇数位置之前
    fn magic_shuffle(nums: &mut Vec<i32>) {
        let n = nums.len();
        if n <= 1 {
            return;
        }
        let mut shuffled = Vec::with_capacity(n);
        // 先放入偶数位置（索引 1,3,5...）
        for i in (1..n).step_by(2) {
            shuffled.push(nums[i]);
        }
        // 再放入奇数位置（索引 0,2,4...）
        for i in (0..n).step_by(2) {
            shuffled.push(nums[i]);
        }
        *nums = shuffled;
    }

    /// 计算两个切片的最长公共前缀长度
    fn common_prefix_len(a: &[i32], b: &[i32]) -> usize {
        a.iter().zip(b).take_while(|(x, y)| x == y).count()
    }
}
```
