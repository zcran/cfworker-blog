---
title: "Leetcode 扫描线1"
date: 2023-12-19T15:44:18+08:00
tags: ["leetcode", "扫描线"]
draft: false
---

## 包含每个查询的最小区间

给你一个二维整数数组 intervals ，其中 intervals[i] = [left⌄i, right⌄i] 表示第 i 个区间开始于 left⌄i 、结束于 right⌄i（包含两侧取值，闭区间）。区间的 长度 定义为区间中包含的整数数目，更正式地表达是 right⌄i - left⌄i + 1 。

再给你一个整数数组 queries 。第 j 个查询的答案是满足 left⌄i <= queries[j] <= right⌄i 的 长度最小区间 i 的长度 。如果不存在这样的区间，那么答案是 -1 。

以数组形式返回对应查询的所有答案。

```
struct Query{
    int val;
    int index;
    int len;
    bool operator < (const Query& q) const{
        return val < q.val;
    }
};
struct Interval{
    int tail;
    int len;
    bool operator < (const Interval& i) const{
        return len > i.len;
    }
};
class Solution {
public:
    vector<int> minInterval(vector<vector<int>>& intervals, vector<int>& queries) {
        sort(intervals.begin(), intervals.end(), [](const vector<int>& a, const vector<int>& b){
            if(a[0] == b[0]){
                return a[1] < b[1];
            }else{
                return a[0] < b[0];
            }
        });
        int tail = 0;
        vector<Query> qVec;
        for(int i = 0; i < queries.size(); ++i){
            qVec.push_back({queries[i], i, 0});
        }
        sort(qVec.begin(), qVec.end());
        priority_queue<Interval> PQ;
        for(auto& it: qVec){
            while(tail < intervals.size()  and intervals[tail][0] <= it.val){                
                PQ.push({intervals[tail][1], intervals[tail][1] - intervals[tail][0] + 1});
                ++tail;
            }
            while(!PQ.empty() and PQ.top().tail < it.val){
                PQ.pop();
            }
            if(PQ.empty()){
                it.len = -1;
            }else{
                it.len = PQ.top().len;    
            }
        }
        vector<int> ret(qVec.size());
        for(auto& it:qVec){
            ret[it.index] = it.len;
        }
        return ret;
    }
};

```