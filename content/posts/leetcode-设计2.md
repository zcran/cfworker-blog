---
title: "leetcode-设计2"
date: 2026-07-14T10:41:24+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 窥视迭代器

请你在设计一个迭代器，在集成现有迭代器拥有的 hasNext 和 next 操作的基础上，还额外支持 peek 操作。

实现 PeekingIterator 类：

PeekingIterator(Iterator<int> nums) 使用指定整数迭代器 nums 初始化迭代器。
int next() 返回数组中的下一个元素，并将指针移动到下个元素处。
bool hasNext() 如果数组中存在下一个元素，返回 true ；否则，返回 false 。
int peek() 返回数组中的下一个元素，但 不 移动指针。

注意：每种语言可能有不同的构造函数和迭代器 Iterator，但均支持 int next() 和 boolean hasNext() 函数。


C++
```
class PeekingIterator : public Iterator {
public:
    /**
     * @brief 使用整数向量初始化 PeekingIterator
     * @param nums 要迭代的整数向量
     *
     * 初始化时提前读取第一个元素，实现"窥视"功能
     */
    PeekingIterator(const vector<int>& nums) : Iterator(nums) {
        // 检查原始迭代器是否有下一个元素
        hasNextElement = Iterator::hasNext();
        if (hasNextElement) {
            // 缓存第一个元素，供 peek() 使用
            cachedNext = Iterator::next();
        }
    }

    /**
     * @brief 返回下一个元素的值，但不移动指针
     * @return 下一个元素的值
     *
     * 时间复杂度: O(1)
     */
    int peek() {
        return cachedNext;
    }

    /**
     * @brief 返回下一个元素的值，并将指针移动到下个位置
     * @return 当前下一个元素的值
     *
     * 时间复杂度: O(1)
     */
    int next() {
        int result = cachedNext; // 保存当前缓存值

        // 预读取下一个元素
        hasNextElement = Iterator::hasNext();
        if (hasNextElement) {
            cachedNext = Iterator::next(); // 缓存下一个元素
        }

        return result;
    }

    /**
     * @brief 检查是否还有下一个元素
     * @return true 如果还有下一个元素，否则 false
     *
     * 时间复杂度: O(1)
     */
    bool hasNext() const {
        return hasNextElement;
    }

private:
    int cachedNext;      // 缓存的下一个元素值（用于 peek）
    bool hasNextElement; // 标记是否还有下一个元素
};
```
