---
title: "leetcode-广度优先搜索14"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 员工的重要性

你有一个保存员工信息的数据结构，它包含了员工唯一的 id ，重要度和直系下属的 id 。

给定一个员工数组 employees，其中：

employees[i].id 是第 i 个员工的 ID。
employees[i].importance 是第 i 个员工的重要度。
employees[i].subordinates 是第 i 名员工的直接下属的 ID 列表。

给定一个整数 id 表示一个员工的 ID，返回这个员工和他所有下属的重要度的 总和。

C++
```
class Solution {
public:
    int getImportance(vector<Employee*> employees, int id) {
        // 建立 id -> Employee* 的映射，便于 O(1) 查找
        unordered_map<int, Employee*> mp;
        mp.reserve(employees.size());
        for (Employee* e : employees) {
            mp[e->id] = e;
        }

        // 迭代 DFS（显式栈），避免递归深度过大
        int total = 0;
        stack<int> st;
        st.push(id);
        while (!st.empty()) {
            Employee* e = mp[st.top()];
            st.pop();
            total += e->importance;
            for (int subId : e->subordinates) {
                st.push(subId);
            }
        }
        return total;
    }
};
```
