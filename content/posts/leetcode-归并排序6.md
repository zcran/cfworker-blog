---
title: "Leetcode 归并排序6"
date: 2025-05-23T21:22:00+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 通过指令创建有序数组

给你一个整数数组 instructions ，你需要根据 instructions 中的元素创建一个有序数组。一开始你有一个空的数组 nums ，你需要 从左到右 遍历 instructions 中的元素，将它们依次插入 nums 数组中。每一次插入操作的 代价 是以下两者的 较小值 ：

nums 中 严格小于  instructions[i] 的数字数目。
nums 中 严格大于  instructions[i] 的数字数目。
比方说，如果要将 3 插入到 nums = [1,2,3,5] ，那么插入操作的 代价 为 min(2, 1) (元素 1 和  2 小于 3 ，元素 5 大于 3 ），插入后 nums 变成 [1,2,3,3,5] 。

请你返回将 instructions 中所有元素依次插入 nums 后的 总最小代价 。由于答案会很大，请将它对 109 + 7 取余 后返回。

示例 1：

输入：instructions = [1,5,6,2]
输出：1
解释：一开始 nums = [] 。
插入 1 ，代价为 min(0, 0) = 0 ，现在 nums = [1] 。
插入 5 ，代价为 min(1, 0) = 0 ，现在 nums = [1,5] 。
插入 6 ，代价为 min(2, 0) = 0 ，现在 nums = [1,5,6] 。
插入 2 ，代价为 min(1, 2) = 1 ，现在 nums = [1,2,5,6] 。
总代价为 0 + 0 + 0 + 1 = 1 。

示例 2:

输入：instructions = [1,2,3,6,5,4]
输出：3
解释：一开始 nums = [] 。
插入 1 ，代价为 min(0, 0) = 0 ，现在 nums = [1] 。
插入 2 ，代价为 min(1, 0) = 0 ，现在 nums = [1,2] 。
插入 3 ，代价为 min(2, 0) = 0 ，现在 nums = [1,2,3] 。
插入 6 ，代价为 min(3, 0) = 0 ，现在 nums = [1,2,3,6] 。
插入 5 ，代价为 min(3, 1) = 1 ，现在 nums = [1,2,3,5,6] 。
插入 4 ，代价为 min(3, 2) = 2 ，现在 nums = [1,2,3,4,5,6] 。
总代价为 0 + 0 + 0 + 0 + 1 + 2 = 3 。

示例 3：

输入：instructions = [1,3,3,3,2,4,2,1,2]
输出：4
解释：一开始 nums = [] 。
插入 1 ，代价为 min(0, 0) = 0 ，现在 nums = [1] 。
插入 3 ，代价为 min(1, 0) = 0 ，现在 nums = [1,3] 。
插入 3 ，代价为 min(1, 0) = 0 ，现在 nums = [1,3,3] 。
插入 3 ，代价为 min(1, 0) = 0 ，现在 nums = [1,3,3,3] 。
插入 2 ，代价为 min(1, 3) = 1 ，现在 nums = [1,2,3,3,3] 。
插入 4 ，代价为 min(5, 0) = 0 ，现在 nums = [1,2,3,3,3,4] 。
​​​​​插入 2 ，代价为 min(1, 4) = 1 ，现在 nums = [1,2,2,3,3,3,4] 。
插入 1 ，代价为 min(0, 6) = 0 ，现在 nums = [1,1,2,2,3,3,3,4] 。
插入 2 ，代价为 min(2, 4) = 2 ，现在 nums = [1,1,2,2,2,3,3,3,4] 。
总代价为 0 + 0 + 0 + 0 + 1 + 0 + 1 + 0 + 2 = 4 。

方法一，归并排序，C++
```
class Solution {
private:
    int numsSorted[100000]; //用于归并的辅助数组，这里值即定义在外面，省的每次归并都要重新申请
    //统计出某个数字左边比它小的数字个数
    void merging(vector<int>& ins, vector<int>& id, vector<int>& leftSmaller, int low, int high)
    {
        //low 是下标开始位置，high 是下标结束位置的下一个位置
        if (low - high >= -1) {
            return;
        }
        int mid = (low + high) >> 1;
        merging(ins, id, leftSmaller, low, mid);
        merging(ins, id, leftSmaller, mid, high);
        int p = 0;
        int q = low, r = mid;
        //非递增归并排序，只移动下标数组，不改变原数组
        while (q < mid && r < high) {
            if (ins[id[q]] < ins[id[r]]) {
                leftSmaller[id[r]] += mid - q;
                numsSorted[p++] = id[r++];
            } else {
                numsSorted[p++] = id[q++];
            }
        }
        while (r < high) {
            numsSorted[p++] = id[r++];
        }
        while (q < mid) {
            numsSorted[p++] = id[q++];
        }
        for (int i = low; i < high; ++i) {
            id[i] = numsSorted[i - low];
        }
        return;
    }
public:
    int createSortedArray(vector<int>& instructions) 
    {
        int mod = 1e9 + 7;
        int n = instructions.size();
        int repeat[100001] = { 0 }; //用于统计某数字左边和它相同的数字个数
        vector<int> id(n);
        for (int i = 0; i < n; ++i) {
            id[i] = i;
        }
        vector<int> leftSmaller(n, 0);
        merging(instructions, id, leftSmaller, 0, n);
        long ans = 0;
        for (int i = 0; i < n; ++i) {
            //总数减去小的再减去相等的剩下的就是大的
            ans += min(leftSmaller[i], i - leftSmaller[i] - repeat[instructions[i]]);
            ++repeat[instructions[i]];
        }
        return ans % mod;
    }
};
```

方法二，树状数组
```
struct BIT {
    arr: Vec<i32>,
}

impl BIT {
    fn new(n: usize) -> Self {
        Self {
            arr: vec![0; n + 1],
        }
    }

    fn update(&mut self, mut i: usize, diff: i32) {
        i += 1;
        while i < self.arr.len() {
            self.arr[i] += diff;
            i += Self::lowbit(i);
        }
    }

    fn query(&self, mut l: usize, mut r: usize) -> i32 {
        self.prefix(r) - self.prefix(l)
    }

    fn prefix(&self, mut i: usize) -> i32 {
        let mut ans = 0;
        while i > 0 {
            ans += self.arr[i];
            i -= Self::lowbit(i);
        }
        ans
    }

    fn lowbit(num: usize) -> usize {
        num & (!num + 1)
    }
}

impl Solution {
    pub fn create_sorted_array(instructions: Vec<i32>) -> i32 {
        let mut bit = BIT::new(100_001);
        let (l, r) = (0, 100_001);
        let mut ans = 0;
        let m = 1_000_000_007;

        for i in instructions {
            ans += bit.query(l, i as usize).min(bit.query(i as usize + 1, r));
            ans %= m;
            bit.update(i as usize, 1);
        }
        ans
    }
}
```