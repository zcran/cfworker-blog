---
title: "Leetcode 随机化13"
date: 2024-12-21T15:03:43+08:00
tags: ["leetcode", "随机化"]
draft: false
---

## O(1) 时间插入、删除和获取随机元素

设计一个支持在平均 时间复杂度 O(1) 下，执行以下操作的数据结构：

insert(val)：当元素 val 不存在时返回 true ，并向集合中插入该项，否则返回 false 。
remove(val)：当元素 val 存在时返回 true ，并从集合中移除该项，否则返回 false 。
getRandom：随机返回现有集合中的一项。每个元素应该有 相同的概率 被返回。




C++代码，解析：变长数组 + 哈希表 
这道题要求实现一个类，满足插入、删除和随机访问元素操作的平均时间复杂度为 O(1)。

变长数组可以在 O(1) 的时间内完成随机访问元素操作，但是由于无法在 O(1) 的时间内判断元素是否存在，因此不能在 O(1) 的时间内完成插入和删除操作。哈希表可以在 O(1) 的时间内完成插入和删除操作，但是由于无法根据下标定位到特定元素，因此不能在 O(1) 的时间内完成随机访问元素操作。为了满足插入、删除和随机访问元素操作的时间复杂度都是 O(1)，需要将变长数组和哈希表结合，变长数组中存储元素，哈希表中存储每个元素在变长数组中的下标。

插入操作时，首先判断 val 是否在哈希表中，如果已经存在则返回 false，如果不存在则插入 val，操作如下：

在变长数组的末尾添加 val；

在添加 val 之前的变长数组长度为 val 所在下标 index，将 val 和下标 index 存入哈希表；

返回 true。

删除操作时，首先判断 val 是否在哈希表中，如果不存在则返回 false，如果存在则删除 val，操作如下：

从哈希表中获得 val 的下标 index；

将变长数组的最后一个元素 last 移动到下标 index 处，在哈希表中将 last 的下标更新为 index；

在变长数组中删除最后一个元素，在哈希表中删除 val；

返回 true。

删除操作的重点在于将变长数组的最后一个元素移动到待删除元素的下标处，然后删除变长数组的最后一个元素。该操作的时间复杂度是 O(1)，且可以保证在删除操作之后变长数组中的所有元素的下标都连续，方便插入操作和随机访问元素操作。

随机访问元素操作时，由于变长数组中的所有元素的下标都连续，因此随机选取一个下标，返回变长数组中该下标处的元素。
```
class RandomizedSet {
public:
    RandomizedSet() {
        srand((unsigned)time(NULL));
    }
    
    bool insert(int val) {
        if (indices.count(val)) {
            return false;
        }
        int index = nums.size();
        nums.emplace_back(val);
        indices[val] = index;
        return true;
    }
    
    bool remove(int val) {
        if (!indices.count(val)) {
            return false;
        }
        int index = indices[val];
        int last = nums.back();
        nums[index] = last;
        indices[last] = index;
        nums.pop_back();
        indices.erase(val);
        return true;
    }
    
    int getRandom() {
        int randomIndex = rand()%nums.size();
        return nums[randomIndex];
    }
private:
    vector<int> nums;
    unordered_map<int, int> indices;
};
```

Rust代码：
```
use rand::Rng;
use std::collections::HashMap;

struct RandomizedSet {
    nums: Vec<i32>,
    indices: HashMap<i32, usize>,
}


/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl RandomizedSet {

    /** Initialize your data structure here. */
    fn new() -> Self {
        Self {
            nums: Vec::new(),
            indices: HashMap::new(),
        }
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    fn insert(&mut self, val: i32) -> bool {
        if self.indices.contains_key(&val) {
           return false;
        }

        let index = self.nums.len();
        self.nums.push(val);
        self.indices.insert(val, index);
        true
    }
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    fn remove(&mut self, val: i32) -> bool {
        if let Some(&index) = self.indices.get(&val) {
            let last_index = self.nums.len() - 1;
            self.nums[index] = self.nums[last_index];
            self.indices.insert(self.nums[last_index], index);
            self.indices.remove(&val);
            self.nums.pop();

            return true;
        }

        false
    }
    
    /** Get a random element from the set. */
    fn get_random(&self) -> i32 {
        let mut rng = rand::thread_rng();
        let index: usize = rng.gen_range(0..self.nums.len());
        self.nums[index]
    }
}

/**
 * Your RandomizedSet object will be instantiated and called as such:
 * let obj = RandomizedSet::new();
 * let ret_1: bool = obj.insert(val);
 * let ret_2: bool = obj.remove(val);
 * let ret_3: i32 = obj.get_random();
 */

```