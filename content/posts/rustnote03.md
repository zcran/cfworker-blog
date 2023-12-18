---
title: "Rust Note 03"
date: 2023-05-19T20:33:16+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 买卖股票的最佳时机2

给你一个整数数组 prices ，其中 prices[i] 表示某支股票第 i 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候 最多 只能持有 一股 股票。你也可以先购买，然后在 同一天 出售。

返回 你能获得的 最大 利润 。


## C 解法 ：
```
public int maxProfit(int[] prices) {
    if (prices == null || prices.length < 2)
        return 0;
    int total = 0, index = 0, length = prices.length;
    while (index < length) {
        //如果股票下跌就一直找，直到找到股票开始上涨为止
        while (index < length - 1 && prices[index] >= prices[index + 1])
            index++;
        //股票上涨开始的值，也就是这段时间上涨的最小值
        int min = prices[index];
        //一直找到股票上涨的最大值为止
        while (index < length - 1 && prices[index] <= prices[index + 1])
            index++;
        //计算这段上涨时间的差值，然后累加
        total += prices[index++] - min;
    }
    return total;
}
```

## Rust 解法1 ：
```
impl Solution {
    pub fn max_profit(prices: Vec<i32>) -> i32 {
       let mut profit = 0;
        for i in 1..prices.len() {
            if prices[i] > prices[i-1]{
                profit += prices[i] - prices[i-1]
            }
        }
        return profit
    }
}
```

## Rust 解法2 ： 
```
impl Solution {
    pub fn max_profit(prices: Vec<i32>) -> i32 {
        prices.windows(2).map(|x| x[1] - x[0]).filter(|&x| x > 0).sum()
    }
}
```

## Rust 解法3 ：
```
impl Solution {
    pub fn max_profit(prices: Vec<i32>) -> i32 {
        let (mut buy, mut sell) = (-prices[0], 0);
        prices.iter().skip(1).for_each(|&v| {
            buy = buy.max(sell - v);
            sell = sell.max(v + buy);
        });
        sell
    }
}
```

## Rust 官方迭代器 ：
```
pub trait Iterator {
    type Item;
    

    fn next(&mut self) -> Option<Self::Item>;

    fn next_chunk<const N: usize>(
        &mut self
    ) -> Result<[Self::Item; N], IntoIter<Self::Item, N>> { ... }
    fn size_hint(&self) -> (usize, Option<usize>) { ... }
    fn count(self) -> usize { ... }
    fn last(self) -> Option<Self::Item> { ... }
    fn advance_by(&mut self, n: usize) -> Result<(), usize> { ... }
    fn nth(&mut self, n: usize) -> Option<Self::Item> { ... }
    fn step_by(self, step: usize) -> StepBy<Self>ⓘ { ... }
    fn chain<U>(self, other: U) -> Chain<Self, <U as IntoIterator>::IntoIter>ⓘ
    where
        U: IntoIterator<Item = Self::Item>,
    { ... }
    fn zip<U>(self, other: U) -> Zip<Self, <U as IntoIterator>::IntoIter>ⓘ
    where
        U: IntoIterator,
    { ... }
    fn intersperse(self, separator: Self::Item) -> Intersperse<Self>ⓘ
    where
        Self::Item: Clone,
    { ... }
    fn intersperse_with<G>(self, separator: G) -> IntersperseWith<Self, G>ⓘ
    where
        G: FnMut() -> Self::Item,
    { ... }
    fn map<B, F>(self, f: F) -> Map<Self, F>ⓘ
    where
        F: FnMut(Self::Item) -> B,
    { ... }
    fn for_each<F>(self, f: F)
    where
        F: FnMut(Self::Item),
    { ... }
    fn filter<P>(self, predicate: P) -> Filter<Self, P>ⓘ
    where
        P: FnMut(&Self::Item) -> bool,
    { ... }
    fn filter_map<B, F>(self, f: F) -> FilterMap<Self, F>ⓘ
    where
        F: FnMut(Self::Item) -> Option<B>,
    { ... }
    fn enumerate(self) -> Enumerate<Self>ⓘ { ... }
    fn peekable(self) -> Peekable<Self>ⓘ { ... }
    fn skip_while<P>(self, predicate: P) -> SkipWhile<Self, P>ⓘ
    where
        P: FnMut(&Self::Item) -> bool,
    { ... }
    fn take_while<P>(self, predicate: P) -> TakeWhile<Self, P>ⓘ
    where
        P: FnMut(&Self::Item) -> bool,
    { ... }
    fn map_while<B, P>(self, predicate: P) -> MapWhile<Self, P>ⓘ
    where
        P: FnMut(Self::Item) -> Option<B>,
    { ... }
    fn skip(self, n: usize) -> Skip<Self>ⓘ { ... }
    fn take(self, n: usize) -> Take<Self>ⓘ { ... }
    fn scan<St, B, F>(self, initial_state: St, f: F) -> Scan<Self, St, F>ⓘ
    where
        F: FnMut(&mut St, Self::Item) -> Option<B>,
    { ... }
    fn flat_map<U, F>(self, f: F) -> FlatMap<Self, U, F>ⓘ
    where
        U: IntoIterator,
        F: FnMut(Self::Item) -> U,
    { ... }
    fn flatten(self) -> Flatten<Self>ⓘ
    where
        Self::Item: IntoIterator,
    { ... }
    fn fuse(self) -> Fuse<Self>ⓘ { ... }
    fn inspect<F>(self, f: F) -> Inspect<Self, F>ⓘ
    where
        F: FnMut(&Self::Item),
    { ... }
    fn by_ref(&mut self) -> &mut Self { ... }
    fn collect<B>(self) -> B
    where
        B: FromIterator<Self::Item>,
    { ... }
    fn try_collect<B>(
        &mut self
    ) -> <<Self::Item as Try>::Residual as Residual<B>>::TryType
    where
        B: FromIterator<<Self::Item as Try>::Output>,
        Self::Item: Try,
        <Self::Item as Try>::Residual: Residual<B>,
    { ... }
    fn collect_into<E>(self, collection: &mut E) -> &mut E
    where
        E: Extend<Self::Item>,
    { ... }
    fn partition<B, F>(self, f: F) -> (B, B)
    where
        B: Default + Extend<Self::Item>,
        F: FnMut(&Self::Item) -> bool,
    { ... }
    fn partition_in_place<'a, T, P>(self, predicate: P) -> usize
    where
        T: 'a,
        Self: DoubleEndedIterator<Item = &'a mut T>,
        P: FnMut(&T) -> bool,
    { ... }
    fn is_partitioned<P>(self, predicate: P) -> bool
    where
        P: FnMut(Self::Item) -> bool,
    { ... }
    fn try_fold<B, F, R>(&mut self, init: B, f: F) -> R
    where
        F: FnMut(B, Self::Item) -> R,
        R: Try<Output = B>,
    { ... }
    fn try_for_each<F, R>(&mut self, f: F) -> R
    where
        F: FnMut(Self::Item) -> R,
        R: Try<Output = ()>,
    { ... }
    fn fold<B, F>(self, init: B, f: F) -> B
    where
        F: FnMut(B, Self::Item) -> B,
    { ... }
    fn reduce<F>(self, f: F) -> Option<Self::Item>
    where
        F: FnMut(Self::Item, Self::Item) -> Self::Item,
    { ... }
    fn try_reduce<F, R>(
        &mut self,
        f: F
    ) -> <<R as Try>::Residual as Residual<Option<<R as Try>::Output>>>::TryType
    where
        F: FnMut(Self::Item, Self::Item) -> R,
        R: Try<Output = Self::Item>,
        <R as Try>::Residual: Residual<Option<Self::Item>>,
    { ... }
    fn all<F>(&mut self, f: F) -> bool
    where
        F: FnMut(Self::Item) -> bool,
    { ... }
    fn any<F>(&mut self, f: F) -> bool
    where
        F: FnMut(Self::Item) -> bool,
    { ... }
    fn find<P>(&mut self, predicate: P) -> Option<Self::Item>
    where
        P: FnMut(&Self::Item) -> bool,
    { ... }
    fn find_map<B, F>(&mut self, f: F) -> Option<B>
    where
        F: FnMut(Self::Item) -> Option<B>,
    { ... }
    fn try_find<F, R>(
        &mut self,
        f: F
    ) -> <<R as Try>::Residual as Residual<Option<Self::Item>>>::TryType
    where
        F: FnMut(&Self::Item) -> R,
        R: Try<Output = bool>,
        <R as Try>::Residual: Residual<Option<Self::Item>>,
    { ... }
    fn position<P>(&mut self, predicate: P) -> Option<usize>
    where
        P: FnMut(Self::Item) -> bool,
    { ... }
    fn rposition<P>(&mut self, predicate: P) -> Option<usize>
    where
        P: FnMut(Self::Item) -> bool,
        Self: ExactSizeIterator + DoubleEndedIterator,
    { ... }
    fn max(self) -> Option<Self::Item>
    where
        Self::Item: Ord,
    { ... }
    fn min(self) -> Option<Self::Item>
    where
        Self::Item: Ord,
    { ... }
    fn max_by_key<B, F>(self, f: F) -> Option<Self::Item>
    where
        B: Ord,
        F: FnMut(&Self::Item) -> B,
    { ... }
    fn max_by<F>(self, compare: F) -> Option<Self::Item>
    where
        F: FnMut(&Self::Item, &Self::Item) -> Ordering,
    { ... }
    fn min_by_key<B, F>(self, f: F) -> Option<Self::Item>
    where
        B: Ord,
        F: FnMut(&Self::Item) -> B,
    { ... }
    fn min_by<F>(self, compare: F) -> Option<Self::Item>
    where
        F: FnMut(&Self::Item, &Self::Item) -> Ordering,
    { ... }
    fn rev(self) -> Rev<Self>ⓘ
    where
        Self: DoubleEndedIterator,
    { ... }
    fn unzip<A, B, FromA, FromB>(self) -> (FromA, FromB)
    where
        FromA: Default + Extend<A>,
        FromB: Default + Extend<B>,
        Self: Iterator<Item = (A, B)>,
    { ... }
    fn copied<'a, T>(self) -> Copied<Self>ⓘ
    where
        T: 'a + Copy,
        Self: Iterator<Item = &'a T>,
    { ... }
    fn cloned<'a, T>(self) -> Cloned<Self>ⓘ
    where
        T: 'a + Clone,
        Self: Iterator<Item = &'a T>,
    { ... }
    fn cycle(self) -> Cycle<Self>ⓘ
    where
        Self: Clone,
    { ... }
    fn sum<S>(self) -> S
    where
        S: Sum<Self::Item>,
    { ... }
    fn product<P>(self) -> P
    where
        P: Product<Self::Item>,
    { ... }
    fn cmp<I>(self, other: I) -> Ordering
    where
        I: IntoIterator<Item = Self::Item>,
        Self::Item: Ord,
    { ... }
    fn cmp_by<I, F>(self, other: I, cmp: F) -> Ordering
    where
        I: IntoIterator,
        F: FnMut(Self::Item, <I as IntoIterator>::Item) -> Ordering,
    { ... }
    fn partial_cmp<I>(self, other: I) -> Option<Ordering>
    where
        I: IntoIterator,
        Self::Item: PartialOrd<<I as IntoIterator>::Item>,
    { ... }
    fn partial_cmp_by<I, F>(self, other: I, partial_cmp: F) -> Option<Ordering>
    where
        I: IntoIterator,
        F: FnMut(Self::Item, <I as IntoIterator>::Item) -> Option<Ordering>,
    { ... }
    fn eq<I>(self, other: I) -> bool
    where
        I: IntoIterator,
        Self::Item: PartialEq<<I as IntoIterator>::Item>,
    { ... }
    fn eq_by<I, F>(self, other: I, eq: F) -> bool
    where
        I: IntoIterator,
        F: FnMut(Self::Item, <I as IntoIterator>::Item) -> bool,
    { ... }
    fn ne<I>(self, other: I) -> bool
    where
        I: IntoIterator,
        Self::Item: PartialEq<<I as IntoIterator>::Item>,
    { ... }
    fn lt<I>(self, other: I) -> bool
    where
        I: IntoIterator,
        Self::Item: PartialOrd<<I as IntoIterator>::Item>,
    { ... }
    fn le<I>(self, other: I) -> bool
    where
        I: IntoIterator,
        Self::Item: PartialOrd<<I as IntoIterator>::Item>,
    { ... }
    fn gt<I>(self, other: I) -> bool
    where
        I: IntoIterator,
        Self::Item: PartialOrd<<I as IntoIterator>::Item>,
    { ... }
    fn ge<I>(self, other: I) -> bool
    where
        I: IntoIterator,
        Self::Item: PartialOrd<<I as IntoIterator>::Item>,
    { ... }
    fn is_sorted(self) -> bool
    where
        Self::Item: PartialOrd<Self::Item>,
    { ... }
    fn is_sorted_by<F>(self, compare: F) -> bool
    where
        F: FnMut(&Self::Item, &Self::Item) -> Option<Ordering>,
    { ... }
    fn is_sorted_by_key<F, K>(self, f: F) -> bool
    where
        F: FnMut(Self::Item) -> K,
        K: PartialOrd<K>,
    { ... }
}
```