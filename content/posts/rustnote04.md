---
title: "Rust Note 04"
date: 2023-05-19T21:35:29+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
## 存在重复元素

给你一个整数数组 nums 。如果任一值在数组中出现 至少两次 ，返回 true ；如果数组中每个元素互不相同，返回 false 。

## C 解法 ：
```
public boolean containsDuplicate(int[] nums) {
    Arrays.sort(nums);
    for (int ind = 1; ind < nums.length; ind++) {
        if (nums[ind] == nums[ind - 1]) {
            return true;
        }
    }
    return false;
}
```

## Rust 解法 1 :
```
impl Solution {
    pub fn contains_duplicate(mut nums: Vec<i32>) -> bool {
        nums.sort();
        (&nums).windows(2).fold(false, |mut res,y|{
            (y[0] == y[1]) | res
        })
    }
}
```

## Rust 解法 2 ：
```
impl Solution {
    pub fn contains_duplicate(nums: Vec<i32>) -> bool {
        let mut vec = nums.to_owned();
        let l = vec.len();
        vec.sort();
        vec.dedup();
        l != vec.len()
    }
}
```

## Rust 解法 3 ：
```
use std::collections::HashSet;
impl Solution {
    pub fn contains_duplicate(nums: Vec<i32>) -> bool {
        nums.len() != nums.into_iter().collect::<HashSet<i32>>().len()
    }
}
```

## Rust windows() 函数 ：
pub fn windows(&self, size: usize) -> Windows<'\_, T>ⓘ

返回长度为 `size` 的所有连续 windows 上的迭代器。 windows 重叠。 如果切片短于 `size`，则迭代器不返回任何值。

##### Panics ：

如果 `size` 为 0，就会出现 panics。

##### Examples ：

```
let slice = ['r', 'u', 's', 't'];
let mut iter = slice.windows(2);
assert_eq!(iter.next().unwrap(), &['r', 'u']);
assert_eq!(iter.next().unwrap(), &['u', 's']);
assert_eq!(iter.next().unwrap(), &['s', 't']);
assert!(iter.next().is_none());
```

如果切片短于 `size`：

```
let slice = ['f', 'o', 'o'];
let mut iter = slice.windows(4);
assert!(iter.next().is_none());
```

##### source ：
```
#[derive(Debug)]
#[stable(feature = "rust1", since = "1.0.0")]
pub struct Windows<'a, T: 'a> {
    v: &'a [T],
    size: NonZeroUsize,
}

impl<'a, T: 'a> Windows<'a, T> {
    #[inline]
    pub(super) fn new(slice: &'a [T], size: NonZeroUsize) -> Self {
        Self { v: slice, size }
    }
}

// FIXME(#26925) 删除以支持 `#[derive(Clone)]`
#[stable(feature = "rust1", since = "1.0.0")]
impl<T> Clone for Windows<'_, T> {
    fn clone(&self) -> Self {
        Windows { v: self.v, size: self.size }
    }
}

#[stable(feature = "rust1", since = "1.0.0")]
impl<'a, T> Iterator for Windows<'a, T> {
    type Item = &'a [T];

    #[inline]
    fn next(&mut self) -> Option<&'a [T]> {
        if self.size.get() > self.v.len() {
            None
        } else {
            let ret = Some(&self.v[..self.size.get()]);
            self.v = &self.v[1..];
            ret
        }
    }

    #[inline]
    fn size_hint(&self) -> (usize, Option<usize>) {
        if self.size.get() > self.v.len() {
            (0, Some(0))
        } else {
            let size = self.v.len() - self.size.get() + 1;
            (size, Some(size))
        }
    }

    #[inline]
    fn count(self) -> usize {
        self.len()
    }

    #[inline]
    fn nth(&mut self, n: usize) -> Option<Self::Item> {
        let (end, overflow) = self.size.get().overflowing_add(n);
        if end > self.v.len() || overflow {
            self.v = &[];
            None
        } else {
            let nth = &self.v[n..end];
            self.v = &self.v[n + 1..];
            Some(nth)
        }
    }

    #[inline]
    fn last(self) -> Option<Self::Item> {
        if self.size.get() > self.v.len() {
            None
        } else {
            let start = self.v.len() - self.size.get();
            Some(&self.v[start..])
        }
    }

    #[doc(hidden)]
    unsafe fn __iterator_get_unchecked(&mut self, idx: usize) -> Self::Item {
        // SAFETY: 因为调用者保证 `i` 在范围之内，这意味着 `i` 不会溢出 `isize`，并且 `from_raw_parts` 创建的切片是 `self.v` 的子切片，因此可以保证对于 `self.v` 的生命周期 `'a` 有效。
        //
        //
        //
        unsafe { from_raw_parts(self.v.as_ptr().add(idx), self.size.get()) }
    }
}

#[stable(feature = "rust1", since = "1.0.0")]
impl<'a, T> DoubleEndedIterator for Windows<'a, T> {
    #[inline]
    fn next_back(&mut self) -> Option<&'a [T]> {
        if self.size.get() > self.v.len() {
            None
        } else {
            let ret = Some(&self.v[self.v.len() - self.size.get()..]);
            self.v = &self.v[..self.v.len() - 1];
            ret
        }
    }

    #[inline]
    fn nth_back(&mut self, n: usize) -> Option<Self::Item> {
        let (end, overflow) = self.v.len().overflowing_sub(n);
        if end < self.size.get() || overflow {
            self.v = &[];
            None
        } else {
            let ret = &self.v[end - self.size.get()..end];
            self.v = &self.v[..end - 1];
            Some(ret)
        }
    }
}

#[stable(feature = "rust1", since = "1.0.0")]
impl<T> ExactSizeIterator for Windows<'_, T> {}

#[unstable(feature = "trusted_len", issue = "37572")]
unsafe impl<T> TrustedLen for Windows<'_, T> {}

#[stable(feature = "fused", since = "1.26.0")]
impl<T> FusedIterator for Windows<'_, T> {}

#[doc(hidden)]
#[unstable(feature = "trusted_random_access", issue = "none")]
unsafe impl<'a, T> TrustedRandomAccess for Windows<'a, T> {}

#[doc(hidden)]
#[unstable(feature = "trusted_random_access", issue = "none")]
unsafe impl<'a, T> TrustedRandomAccessNoCoerce for Windows<'a, T> {
    const MAY_HAVE_SIDE_EFFECT: bool = false;
}
```
