---
title: "Leetcode 归并排序7"
date: 2025-05-23T21:22:00+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 统计数组中好三元组数目

给你两个下标从 0 开始且长度为 n 的整数数组 nums1 和 nums2 ，两者都是 [0, 1, ..., n - 1] 的 排列 。

好三元组 指的是 3 个 互不相同 的值，且它们在数组 nums1 和 nums2 中出现顺序保持一致。换句话说，如果我们将 pos1v 记为值 v 在 nums1 中出现的位置，pos2v 为值 v 在 nums2 中的位置，那么一个好三元组定义为 0 <= x, y, z <= n - 1 ，且 pos1x < pos1y < pos1z 和 pos2x < pos2y < pos2z 都成立的 (x, y, z) 。

请你返回好三元组的 总数目 。

示例 1：

输入：nums1 = [2,0,1,3], nums2 = [0,1,2,3]
输出：1
解释：
总共有 4 个三元组 (x,y,z) 满足 pos1x < pos1y < pos1z ，分别是 (2,0,1) ，(2,0,3) ，(2,1,3) 和 (0,1,3) 。
这些三元组中，只有 (0,1,3) 满足 pos2x < pos2y < pos2z 。所以只有 1 个好三元组。

示例 2：

输入：nums1 = [4,0,1,3,2], nums2 = [4,1,0,2,3]
输出：4
解释：总共有 4 个好三元组 (4,0,3) ，(4,0,2) ，(4,1,3) 和 (4,1,2) 。
 

方法一，归并排序，C++
```
class Solution {
    vector<int> index;
    vector<int> tempIndex;
    vector<int> temp;
    vector<int> leftLess;
    vector<int> rightGreater;
public:
    void merge(vector<int>& nums, int left, int mid, int right) {
        int i = left;
        int j = mid + 1;
        int cur = left;
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) {
                temp[cur] = nums[i];
                tempIndex[cur] = index[i];
                rightGreater[index[i]] += right - j + 1;
                ++i;
            } else {
                temp[cur] = nums[j];
                tempIndex[cur] = index[j];
                leftLess[index[j]] += i - left;
                ++j;
            }
            ++cur;
        }
        while (i <= mid) {
            temp[cur] = nums[i];
            tempIndex[cur] = index[i];
            ++i;
            ++cur;
        }
        while (j <= right) {
            temp[cur] = nums[j];
            tempIndex[cur] = index[j];
            leftLess[index[j]] += mid - left + 1;
            ++j;
            ++cur;
        }

        for (int k = left; k <= right; ++k) {
            nums[k] = temp[k];
            index[k] = tempIndex[k];
        }
    }

    void mergeSort(vector<int>& nums, int left, int right) {
        if (left >= right) {
            return;
        }
        int mid = (left + right) / 2;
        mergeSort(nums, left, mid);
        mergeSort(nums, mid + 1, right);
        merge(nums, left, mid, right);
    }

    long long goodTriplets(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();
        index.resize(n);
        temp.resize(n);
        tempIndex.resize(n);
        leftLess.resize(n);
        rightGreater.resize(n);

        vector<int> pos2(n);
        vector<int> indexMapping(n);
        for (int i = 0; i < n; ++i) {
            pos2[nums2[i]] = i;
        }
        for (int i = 0; i < n; ++i) {
            indexMapping[i] = pos2[nums1[i]];
            index[i] = i;
        }
        mergeSort(indexMapping, 0, n - 1);
        long long ans = 0;
        for (int i = 0; i < n; ++i) {
            ans += (long long)leftLess[i] * rightGreater[i];
        }
        return ans;
    }
};
```

方法二，树状数组
```
struct FenwickTree {
    tree: Vec<i32>,
}

impl FenwickTree {
    fn new(size: usize) -> Self {
        FenwickTree {
            tree: vec![0; size + 1],
        }
    }

    fn update(&mut self, index: usize, delta: i32) {
        let mut idx = index + 1;
        while idx < self.tree.len() {
            self.tree[idx] += delta;
            idx += idx & (!idx + 1);
        }
    }

    fn query(&self, index: usize) -> i32 {
        let mut idx = index + 1;
        let mut res = 0;
        while idx > 0 {
            res += self.tree[idx];
            idx -= idx & (!idx + 1);
        }
        res
    }
}

impl Solution {
    pub fn good_triplets(nums1: Vec<i32>, nums2: Vec<i32>) -> i64 {
        let n = nums1.len();
        let mut pos2 = vec![0; n];
        for (i, &num) in nums2.iter().enumerate() {
            pos2[num as usize] = i;
        }
        let mut reversed_index_mapping = vec![0; n];
        for (i, &num) in nums1.iter().enumerate() {
            reversed_index_mapping[pos2[num as usize]] = i;
        }
        let mut tree = FenwickTree::new(n);
        let mut res = 0i64;
        for value in 0..n {
            let pos = reversed_index_mapping[value];
            let left = tree.query(pos) as i64;
            tree.update(pos, 1);
            let right = (n - 1 - pos) as i64 - (value as i64 - left);
            res += left * right;
        }
        res
    }
}
```

豆包：
在算法问题中，** 树状数组（Fenwick Tree）和归并排序（Merge Sort）** 看似属于不同的技术范畴（前者是数据结构，后者是排序算法），但它们在解决问题时的深层联系主要体现在 逆序对统计 和 分治思想 的核心逻辑上。以下从多个维度解析它们的内在关联：

一、共同目标：高效统计元素的相对顺序信息
两者的核心价值均在于 高效处理元素的顺序关系，尤其是在需要统计 “比当前元素小 / 大的元素数量”“逆序对数量” 等场景中。
1. 逆序对统计的本质
归并排序：在归并过程中，当右子数组的元素小于左子数组的元素时，右子数组当前元素左侧（左子数组中未处理的元素）的所有元素均与该元素构成逆序对。通过分治递归和归并操作，可在 O(nlogn) 时间内统计所有逆序对。
树状数组：通过将元素离散化后，从左到右遍历数组，每次查询当前元素之前有多少个已出现的元素比它小（或大），从而统计逆序对。核心操作是 单点更新 和 前缀和查询，时间复杂度同样为 O(nlogn)。
2. 顺序关系的抽象建模
两者均将 “元素的相对顺序” 转化为 可计算的数值关系：
归并排序通过分治过程中的左右子数组比较，直接累计顺序信息（如左子数组剩余元素数、右子数组当前元素的位置等）。
树状数组通过维护一个 “已处理元素的频率数组”，利用前缀和快速查询顺序统计量（如比当前元素小的元素个数）。

二、分治思想的不同体现
1. 归并排序：显式分治
归并排序的分治是 递归划分区间 的过程：
将数组分成左右两半，递归处理子问题，最后合并结果。
在合并阶段，通过双指针遍历左右子数组，直接统计跨区间的顺序信息（如逆序对）。
2. 树状数组：隐式分治
树状数组的分治思想隐含在其 二进制分层结构 中：
树状数组通过二进制位将数组划分为多个区间（如长度为 2^k 的块），每个节点维护一个区间的和。
单点更新和前缀查询操作本质上是在这些分层区间中进行 “分治求和”，例如查询前缀和时，通过二进制分解将区间拆分为若干个块的和。
联系：两者均通过 “分解问题→处理子问题→合并结果” 的分治逻辑，将 O(n^2 ) 的暴力统计优化为 O(nlogn)的高效算法。

三、空间与时间的权衡
1. 归并排序：空间换时间
需要额外的临时数组存储合并过程中的元素（空间复杂度 O(n)）。
时间复杂度稳定为 O(nlogn)，适用于大规模数据排序和逆序对统计。
2. 树状数组：时间换空间
空间复杂度为 O(n)，但常数因子较小（仅需一个数组）。
适用于动态更新和查询的场景（如在线算法），但需要离散化处理非连续元素。
联系：两者均通过结构化的方法（排序分治或数据结构分层），在时间与空间之间找到最优平衡点，避免暴力枚举的低效性。

四、典型应用场景的交叉
两者常共同出现在需要 二维或多维顺序统计 的问题中，例如：
二维平面中的点数统计：给定平面上的点集，统计每个点左下方（x<a 且 y<b）的点数。
方法 1：按 x 排序后，对 y 坐标使用树状数组统计前缀和（类似逆序对问题）。
方法 2：使用归并排序的分治思想，在排序过程中维护 y 的顺序信息。
数组元素的排名统计：如统计每个元素左侧比它小的元素个数（左小值）和右侧比它大的元素个数（右大值），这正是前面 “好三元组” 问题的核心需求。

五、深层数学本质：基于比较的顺序统计
两者均依赖于 元素的比较操作，解决的核心问题是：在有序结构中快速定位元素的相对位置。
归并排序通过排序过程显式建立元素的全局顺序。
树状数组通过离散化和前缀和隐式维护元素的顺序关系。
两者的时间复杂度下界均与比较排序的下界 O(nlogn)一致，表明它们在处理基于比较的顺序统计问题时已接近理论最优。

总结：
从 “逆序对” 到 “分治” 的统一逻辑
树状数组和归并排序的深层联系源于它们对 顺序统计问题 的分治优化思路：
归并排序 用排序分治 + 合并阶段统计的方式，直接利用比较过程累计顺序信息。
树状数组 用数据结构分治 + 前缀查询的方式，间接通过离散化和分层求和统计顺序信息。

两者的选择取决于问题的具体需求：

若需要排序结果，归并排序更直接；
若需要动态更新或更紧凑的空间，树状数组更高效。
这种 “分治 + 顺序统计” 的思想贯穿于许多经典算法中，是解决各类排列、组合、统计问题的核心工具。