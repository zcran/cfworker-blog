---
title: "Rust Note 01"
date: 2023-05-18T19:58:49+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
# 删除排序数组中的重复项

给你一个 升序排列 的数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。然后返回 nums 中唯一元素的个数。

考虑 nums 的唯一元素的数量为 k ，你需要做以下事情确保你的题解可以被通过：

更改数组 nums ，使 nums 的前 k 个元素包含唯一元素，并按照它们最初在 nums 中出现的顺序排列。nums 的其余元素与 nums 的大小不重要。
返回 k 。

## C 解法：

```
//双指针解决
public int removeDuplicates(int[] A) {
    //边界条件判断
    if (A == null || A.length == 0)
        return 0;
    int left = 0;
    for (int right = 1; right < A.length; right++)
        //如果左指针和右指针指向的值一样，说明有重复的，
        //这个时候，左指针不动，右指针继续往右移。如果他俩
        //指向的值不一样就把右指针指向的值往前挪
        if (A[left] != A[right])
            A[++left] = A[right];
    return ++left;
}
```


## Rust 解法1：
```
impl Solution {
    pub fn remove_duplicates(nums: &mut Vec<i32>) -> i32 {
        if nums.len() < 2{//数组长度为0，1时候，直接返回数组长度即可
            return nums.len() as i32;
        }
        let mut index = 0;
        for i in 0..nums.len(){
            //因为是递增的数组，每次都只需要和前面部分不重复数组的最后一个元素进行比较
            if(nums[i]!=nums[index]){
                index+=1;
                nums[index] = nums[i];
            }
        }
        index as i32 +1
    }
}
```

## Rust 解法2 ：
```
impl Solution {
    pub fn remove_duplicates(nums: &mut Vec<i32>) -> i32 {
        nums.dedup();
        nums.len() as i32
    }
}
```

## Rust dedup() 标准库函数 ：
pub fn dedup(&mut self)

根据PartialEq特性实现移除向量中连续重复的元素。如果对向量进行排序，则会删除所有重复项。

Examples：
```
let mut vec = vec![1, 2, 2, 3, 2];
vec.dedup();
assert_eq!(vec, [1, 2, 3, 2]);
```

source：
```
#[stable(feature = "rust1", since = "1.0.0")]
#[inline]
pub fn dedup(&mut self) {
    self.dedup_by(|a, b| a == b)
}
```

## Rust dedup_by() 函数 ：
pub fn dedup\_by<F>(&mut self, same\_bucket: F) 
where  
    F: FnMut(&mut T, &mut T) -> bool, 

移除满足给定等式关系的向量中除第一个元素之外的所有连续元素。

“same_bucket”函数传递对向量中两个元素的引用，并且必须确定这些元素是否相等。元素的传递顺序与它们在切片中的顺序相反，因此如果`same_bucket（a，b）`返回`true`，`a`将被删除。

如果对向量进行排序，则会删除所有重复项。
##### Examples:

```
let mut vec = vec!["foo", "bar", "Bar", "baz", "bar"];

vec.dedup_by(|a, b| a.eq_ignore_ascii_case(b));

assert_eq!(vec, ["foo", "bar", "baz", "bar"]);
```

source :
```
#[stable(feature = "dedup_by", since = "1.16.0")]
pub fn dedup_by<F>(&mut self, mut same_bucket: F)
where
    F: FnMut(&mut T, &mut T) -> bool,
{
    let len = self.len();
    if len <= 1 {
        return;
    }

    /* 不变量INVARIANT: vec.len() > read >= write > write-1 >= 0 */
    struct FillGapOnDrop<'a, T, A: core::alloc::Allocator> {
        /* 要检查的元素的偏移量是否重复*/
        read: usize,

        /* 找到非重复项时，要放置非重复项的位置的偏移量。 */
        write: usize,

        /* 如果“same_bucket”惊慌失措，需要纠正的兽医，哈哈哈 */
        vec: &'a mut Vec<T, A>,
    }

    impl<'a, T, A: core::alloc::Allocator> Drop for FillGapOnDrop<'a, T, A> {
        fn drop(&mut self) {
            /* 此代码在`same_bucket`恐慌时执行 */

            /* SAFETY: 不变量保证“read-write”和“len-read”永远不会溢出，并且副本总是在边界内。 */
            unsafe {
                let ptr = self.vec.as_mut_ptr();
                let len = self.vec.len();

                /*当`same_bucket`恐慌时，还剩多少项。
                 * 基本上是vec[read..].len() */
                let items_left = len.wrapping_sub(self.read);

                /* 指向vec[write..write+items_left]切片中第一项的指针 */
                let dropped_ptr = ptr.add(self.write);
                /* 指向vec[read..]切片中第一项的指针 */
                let valid_ptr = ptr.add(self.read);

                /* 将`vec[read..]`复制到`vec[write..write+items_left]`。
                 * 切片可以重叠，因此不能使用“copy_nonoverlapping” */
                ptr::copy(valid_ptr, dropped_ptr, items_left);

                /* 已经丢弃了多少项
                 * 基本上是  vec[read..write].len() */
                let dropped = self.read.wrapping_sub(self.write);

                self.vec.set_len(len - dropped);
            }
        }
    }

    let mut gap = FillGapOnDrop { read: 1, write: 1, vec: self };
    let ptr = gap.vec.as_mut_ptr();

    /* 在执行Vec时删除项目，这应该比执行slice partition_dedup + truncate更有效*/

    /* SAFETY: 由于不变量，read_ptr、prev_ptr和write_ptr始终在边界内，read_ptr从不别名prev_ptr */
    unsafe {
        while gap.read < len {
            let read_ptr = ptr.add(gap.read);
            let prev_ptr = ptr.add(gap.write.wrapping_sub(1));

            if same_bucket(&mut *read_ptr, &mut *prev_ptr) {
                // Increase `gap.read` now since the drop may panic.
                gap.read += 1;
                /* We have found duplicate, drop it in-place */
                ptr::drop_in_place(read_ptr);
            } else {
                let write_ptr = ptr.add(gap.write);

                /* Because `read_ptr` can be equal to `write_ptr`, we either
                 * have to use `copy` or conditional `copy_nonoverlapping`.
                 * Looks like the first option is faster. */
                ptr::copy(read_ptr, write_ptr, 1);

                /* We have filled that place, so go further */
                gap.write += 1;
                gap.read += 1;
            }
        }

        /* 从技术上讲，我们可以让“gap”用它的Drop清理干净，但当“same_bucket”保证不会惊慌时，这会让代码有点膨胀，所以我们只需手动完成 */
        gap.vec.set_len(gap.write);
        mem::forget(gap);
    }
}
```

## Rust copy_nonoverlapping() 函数 ：
pub unsafe fn copy_nonoverlapping<T>(src: *const T, dst: *mut T, count: usize)


将 `count * size_of::<T>()` 字节从 `src` 复制到 `dst`。源和目标必须不重叠。

对于可能重叠的内存区域，请改用 `copy`。

`copy_nonoverlapping` 在语义上等同于 C 的 `memcpy`，但交换了参数顺序。

Safety :
-------------------------------------------------------------------------------

如果违反以下任一条件，则行为是未定义的：

-   对于 `count * size_of::<T>()` 字节的读取，`src` 必须是 `valid`。
    
-   对于 `count * size_of::<T>()` 字节的写入，`dst` 必须是 `valid`。
    
-   `src` 和 `dst` 必须正确对齐。
    
-   从 `src` 开始的内存区域，大小为 `count * size_of::<T> ()` 字节不得与以 `dst` 开始且大小相同的内存区域重叠。
    

与 `read` 一样，无论 `T` 是否为 `Copy`，`copy_nonoverlapping` 都会创建 `T` 的按位副本。 如果 `T` 不是 `Copy`，则使用两个以 `*src`开头的区域和以 `*dst` 开头的区域中的值可以 `违反内存安全`。

请注意，即使有效复制的大小 (`count * size_of::<T>()`) 是 `0`，指针也必须非空的并且正确对齐。

Examples：
-----------------------------------------------------------------------------------

手动实现 `Vec::append`

```
use std::ptr;

/// 将 `src` 的所有元素移到 `dst`，将 `src` 留空。
fn append<T>(dst: &mut Vec<T>, src: &mut Vec<T>) {
    let src_len = src.len();
    let dst_len = dst.len();

    // 确保 `dst` 具有足够的容量来容纳所有 `src`。
    dst.reserve(src_len);

    unsafe {
        // 偏移的调用始终是安全的，因为 `Vec` 分配的字节数永远不会超过 `isize::MAX` 字节。
        let dst_ptr = dst.as_mut_ptr().offset(dst_len as isize);
        let src_ptr = src.as_ptr();

        // 截断 `src` 而不丢弃其内容。
        // 我们首先执行此操作，以避免在 panics 处出现问题时避免出现问题。
        src.set_len(0);

        // 这两个区域不能重叠，因为可变引用没有别名，并且两个不同的 vectors 不能拥有相同的内存。
        ptr::copy_nonoverlapping(src_ptr, dst_ptr, src_len);

        // 通知 `dst` 现在包含 `src` 的内容。
        dst.set_len(dst_len + src_len);
    }
}

let mut a = vec!['r'];
let mut b = vec!['u', 's', 't'];

append(&mut a, &mut b);

assert_eq!(a, &['r', 'u', 's', 't']);
assert!(b.is_empty());
```

source：
-----------------------------------------------------------------------------------
```
#[doc(alias = "memcpy")]
#[stable(feature = "rust1", since = "1.0.0")]
#[rustc_const_unstable(feature = "const_intrinsic_copy", issue = "80697")]
#[inline]
pub const unsafe fn copy_nonoverlapping<T>(src: *const T, dst: *mut T, count: usize) {
    extern "rust-intrinsic" {
        #[rustc_const_unstable(feature = "const_intrinsic_copy", issue = "80697")]
        pub fn copy_nonoverlapping<T>(src: *const T, dst: *mut T, count: usize);
    }

    #[cfg(debug_assertions)]
    fn runtime_check<T>(src: *const T, dst: *mut T, count: usize) {
        if !is_aligned_and_not_null(src)
            || !is_aligned_and_not_null(dst)
            || !is_nonoverlapping(src, dst, count)
        {
            // 不要 panic，以保持代码生成的影响较小。
            abort();
        }
    }
    #[cfg(debug_assertions)]
    const fn compiletime_check<T>(_src: *const T, _dst: *mut T, _count: usize) {}
    #[cfg(debug_assertions)]
    // SAFETY: 运行时调试断言是最好的努力基础； 在编译时不执行这些操作也没关系
    //
    unsafe {
        const_eval_select((src, dst, count), compiletime_check, runtime_check);
    }

    // SAFETY: 调用者必须遵守 `copy_nonoverlapping` 的安全保证。
    //
    unsafe { copy_nonoverlapping(src, dst, count) }
}
```