---
title: "Rust Note 05"
date: 2023-05-19T21:35:34+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
## 只出现一次的数字

给你一个 非空 整数数组 nums ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。

## C解法 ：
```
int singleNumber(int* nums, int numsSize){
    int a = 0;
    for (int i = 0; i < numsSize; i++)
    {
        a = a ^ nums[i];
    }
    return a;
}
```

## Rust 解法 ：
```
impl Solution {
    pub fn single_number(nums: Vec<i32>) -> i32 {
    nums.iter().fold(0,|acc,x| acc^x)
    }
}
```

## Rust fold() 函数 ：
fn fold<B, F>(self, init: B, f: F) -> B where  
    F: FnMut(B, Self::Item) -> B, 

通过应用操作将每个元素 `fold` 到一个累加器中，返回最终结果。

`fold()` 有两个参数：一个初始值，一个闭包，有两个参数：一个 ‘accumulator’ 和一个元素。 闭包返回累加器在下一次迭代中应具有的值。

初始值是累加器在第一次调用时将具有的值。

在将此闭包应用于迭代器的每个元素之后，`fold()` 返回累加器。

该操作有时称为 ‘reduce’ 或 ‘inject’。

当您拥有某个集合，并且希望从中产生单个值时，`fold` 非常有用。

Note: `fold()` 和遍历整个迭代器的类似方法可能不会因无限迭代器而终止，即使在 traits 上，其结果在有限时间内是可确定的。

Note: 如果累加器类型和项类型相同，则可以使用 `reduce()`将第一个元素用作初始值。

Note: `fold()` 以*左关联*方式组合元素。 对于像 `+` 这样的关联性，元素组合的顺序并不重要，但对于像 `-` 这样的非关联性，顺序会影响最终结果。 对于 `fold()` 的*右关联*版本，请参见 `DoubleEndedIterator::rfold()`。

就此而言，其他几种 (forward) 方法都具有默认实现，因此，如果它可以做得比默认 `for` 循环实现更好，请尝试显式实现此方法。

特别是，请尝试将此 `fold()` 放在组成此迭代器的内部部件上。

基本用法：

```
let a = [1, 2, 3];

// 数组所有元素的总和
let sum = a.iter().fold(0, |acc, x| acc + x);

assert_eq!(sum, 6);
```

让我们在这里遍历迭代的每个步骤：

| element | acc | x | result |
| --- | --- | --- | --- |
|  | 0 |  |  |
| 1 | 0 | 1 | 1 |
| 2 | 1 | 2 | 3 |
| 3 | 3 | 3 | 6 |

所以，我们的最终结果，`6`。

这个例子演示了 `fold()` 的左关联特性： 它构建一个字符串，从一个初始值开始，从前面到后面的每个元素继续：

```
let numbers = [1, 2, 3, 4, 5];

let zero = "0".to_string();

let result = numbers.iter().fold(zero, |acc, &x| {
    format!("({} + {})", acc, x)
});

assert_eq!(result, "(((((0 + 1) + 2) + 3) + 4) + 5)");
```

对于那些不经常使用迭代器的人，通常会使用 `for` 循环并附带一系列要建立结果的列表。那些可以变成 `fold ()` s：

```
let numbers = [1, 2, 3, 4, 5];

let mut result = 0;

// for 循环：
for i in &numbers {
    result = result + i;
}

// fold:
let result2 = numbers.iter().fold(0, |acc, &x| acc + x);

// 他们是一样的
assert_eq!(result, result2);
```

#### source :
```
#[doc(alias = "inject", alias = "foldl")]
#[inline]
#[stable(feature = "rust1", since = "1.0.0")]
fn fold<B, F>(mut self, init: B, mut f: F) -> B
where
    Self: Sized,
    F: FnMut(B, Self::Item) -> B,
{
    let mut accum = init;
    while let Some(x) = self.next() {
        accum = f(accum, x);
    }
    accum
}
```