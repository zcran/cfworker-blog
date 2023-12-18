---
title: "Rust Note 12"
date: 2023-05-22T20:28:46+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
## 反转字符串

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 s 的形式给出。

不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。


### C 解法：
```
void swap(char *a, char *b) {
    char t = *a;
    *a = *b, *b = t;
}

void reverseString(char *s, int sSize) {
    for (int left = 0, right = sSize - 1; left < right; ++left, --right) {
        swap(s + left, s + right);
    }
}
```


### Rust 解法：
```
impl Solution {
    pub fn reverse_string(s: &mut Vec<char>) {
        //let sl = s.len();
        //for i in 0..(sl / 2) {
            //let c = s[i];
            //let j = sl - i - 1;
            //s[i] = s[j];
            //s[j] = c;
        //}
        s.reverse();  // 运算稍慢，内存占用很少
    }
}
```

### Rust reverse()函数
slice::reverse
pub fn reverse(&mut self)
适当地反转切片中元素的顺序。

Examples：
```
let mut v = [1, 2, 3];
v.reverse();
assert!(v == [3, 2, 1]);
```

source: 
```
#[stable(feature = "rust1", since = "1.0.0")]
    #[inline]
    pub fn reverse(&mut self) {
        let half_len = self.len() / 2;
        let Range { start, end } = self.as_mut_ptr_range();

        // 这些切片将跳过奇数长度的中间项，因为该项不需要移动。
        //
        let (front_half, back_half) =
            // SAFETY: 两者都是原始切片的子部分，因此内存范围是有效的，并且它们不会重叠，因为它们每个都只是原始切片的一半 (或更少)。
            //
            //
            unsafe {
                (
                    slice::from_raw_parts_mut(start, half_len),
                    slice::from_raw_parts_mut(end.sub(half_len), half_len),
                )
            };

        // 这里引入函数边界意味着两个部分得到 `noalias` 标记，允许更好的优化，因为 LLVM 知道它们是不相交的，不像在原始切片中那样。
        //
        //
        revswap(front_half, back_half, half_len);

        #[inline]
        fn revswap<T>(a: &mut [T], b: &mut [T], n: usize) {
            debug_assert_eq!(a.len(), n);
            debug_assert_eq!(b.len(), n);

            // 因为这个函数首先是独立编译的，所以这个检查告诉 LLVM 下面的索引是在边界内的。
            // 然后在内联之后 -- 一旦知道了切片的实际长度 -- 它就会被删除。
            //
            //
            let (a, b) = (&mut a[..n], &mut b[..n]);

            for i in 0..n {
                mem::swap(&mut a[i], &mut b[n - 1 - i]);
            }
        }
    }
```


### Rust reverse结构体
Struct std::cmp::Reverse
用于逆序排序的辅助结构体。
该结构是一个帮助器，可与 Vec::sort_by_key 等函数一起使用，并且可以用于对键的一部分进行反向排序。

Examples：
```
use std::cmp::Reverse;

let mut v = vec![1, 2, 3, 4, 5, 6];
v.sort_by_key(|&num| (num > 3, Reverse(num)));
assert_eq!(v, vec![3, 2, 1, 6, 5, 4]);
```
