---
title: "Rust Note 02"
date: 2023-05-19T20:31:48+08:00
tags: ["rust"]
draft: false
---

## Rust官方迭代器介绍

#### fn [next](https://doc.rust-lang.org/std/iter/trait.Iterator.html#tymethod.next)(&mut self) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>

Advances the iterator and returns the next value.

Returns [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") when iteration is finished. Individual iterator implementations may choose to resume iteration, and so calling `next()` again may or may not eventually start returning [`Some(Item)`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.Some) again at some point.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples)

Basic usage:

```
let a = [1, 2, 3];

let mut iter = a.iter();

// A call to next() returns the next value...
assert_eq!(Some(&1), iter.next());
assert_eq!(Some(&2), iter.next());
assert_eq!(Some(&3), iter.next());

// ... and then None once it's over.
assert_eq!(None, iter.next());

// More calls may or may not return `None`. Here, they always will.
assert_eq!(None, iter.next());
assert_eq!(None, iter.next());
```

[Run](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0A%2F%2F%20A%20call%20to%20next()%20returns%20the%20next%20value...%0Aassert_eq!(Some(%261)%2C%20iter.next())%3B%0Aassert_eq!(Some(%262)%2C%20iter.next())%3B%0Aassert_eq!(Some(%263)%2C%20iter.next())%3B%0A%0A%2F%2F%20...%20and%20then%20None%20once%20it%27s%20over.%0Aassert_eq!(None%2C%20iter.next())%3B%0A%0A%2F%2F%20More%20calls%20may%20or%20may%20not%20return%20%60None%60.%20Here%2C%20they%20always%20will.%0Aassert_eq!(None%2C%20iter.next())%3B%0Aassert_eq!(None%2C%20iter.next())%3B%0A%7D&edition=2021)

Provided Methods
----------------

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#138-142)

#### fn [next\_chunk](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.next_chunk)<const N: [usize](https://doc.rust-lang.org/std/primitive.usize.html)\>(  
    &mut self  
) -> [Result](https://doc.rust-lang.org/std/result/enum.Result.html "enum std::result::Result")<[\[](https://doc.rust-lang.org/std/primitive.array.html)Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")[; N\]](https://doc.rust-lang.org/std/primitive.array.html), [IntoIter](https://doc.rust-lang.org/std/array/struct.IntoIter.html "struct std::array::IntoIter")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), N>>

🔬 This is a nightly-only experimental API. (`iter_next_chunk` [#98326](https://github.com/rust-lang/rust/issues/98326))

Advances the iterator and returns an array containing the next `N` values.

If there are not enough elements to fill the array then `Err` is returned containing an iterator over the remaining elements.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-1)

Basic usage:

```
#![feature(iter_next_chunk)]

let mut iter = "lorem".chars();

assert_eq!(iter.next_chunk().unwrap(), ['l', 'o']);              // N is inferred as 2
assert_eq!(iter.next_chunk().unwrap(), ['r', 'e', 'm']);         // N is inferred as 3
assert_eq!(iter.next_chunk::<4>().unwrap_err().as_slice(), &[]); // N is explicitly 4
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_next_chunk)%5D%0A%0Afn%20main()%20%7B%0Alet%20mut%20iter%20%3D%20%22lorem%22.chars()%3B%0A%0Aassert_eq!(iter.next_chunk().unwrap()%2C%20%5B%27l%27%2C%20%27o%27%5D)%3B%20%20%20%20%20%20%20%20%20%20%20%20%20%20%2F%2F%20N%20is%20inferred%20as%202%0Aassert_eq!(iter.next_chunk().unwrap()%2C%20%5B%27r%27%2C%20%27e%27%2C%20%27m%27%5D)%3B%20%20%20%20%20%20%20%20%20%2F%2F%20N%20is%20inferred%20as%203%0Aassert_eq!(iter.next_chunk%3A%3A%3C4%3E().unwrap_err().as_slice()%2C%20%26%5B%5D)%3B%20%2F%2F%20N%20is%20explicitly%204%0A%7D&version=nightly&edition=2021)

Split a string and get the first three items.

```
#![feature(iter_next_chunk)]

let quote = "not all those who wander are lost";
let [first, second, third] = quote.split_whitespace().next_chunk().unwrap();
assert_eq!(first, "not");
assert_eq!(second, "all");
assert_eq!(third, "those");
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_next_chunk)%5D%0A%0Afn%20main()%20%7B%0Alet%20quote%20%3D%20%22not%20all%20those%20who%20wander%20are%20lost%22%3B%0Alet%20%5Bfirst%2C%20second%2C%20third%5D%20%3D%20quote.split_whitespace().next_chunk().unwrap()%3B%0Aassert_eq!(first%2C%20%22not%22)%3B%0Aassert_eq!(second%2C%20%22all%22)%3B%0Aassert_eq!(third%2C%20%22those%22)%3B%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#215)

#### fn [size\_hint](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.size_hint)(&self) -> ([usize](https://doc.rust-lang.org/std/primitive.usize.html), [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<[usize](https://doc.rust-lang.org/std/primitive.usize.html)\>)

Returns the bounds on the remaining length of the iterator.

Specifically, `size_hint()` returns a tuple where the first element is the lower bound, and the second element is the upper bound.

The second half of the tuple that is returned is an `[Option](https://doc.rust-lang.org/std/option/enum.Option.html "Option")<[usize](https://doc.rust-lang.org/std/primitive.usize.html "usize")>`. A [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") here means that either there is no known upper bound, or the upper bound is larger than [`usize`](https://doc.rust-lang.org/std/primitive.usize.html "usize").

##### [Implementation notes](https://doc.rust-lang.org/std/iter/trait.Iterator.html#implementation-notes)

It is not enforced that an iterator implementation yields the declared number of elements. A buggy iterator may yield less than the lower bound or more than the upper bound of elements.

`size_hint()` is primarily intended to be used for optimizations such as reserving space for the elements of the iterator, but must not be trusted to e.g., omit bounds checks in unsafe code. An incorrect implementation of `size_hint()` should not lead to memory safety violations.

That said, the implementation should provide a correct estimation, because otherwise it would be a violation of the trait’s protocol.

The default implementation returns `(0, [None](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None"))` which is correct for any iterator.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-2)

Basic usage:

```
let a = [1, 2, 3];
let mut iter = a.iter();

assert_eq!((3, Some(3)), iter.size_hint());
let _ = iter.next();
assert_eq!((2, Some(2)), iter.size_hint());
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0Aassert_eq!((3%2C%20Some(3))%2C%20iter.size_hint())%3B%0Alet%20_%20%3D%20iter.next()%3B%0Aassert_eq!((2%2C%20Some(2))%2C%20iter.size_hint())%3B%0A%7D&edition=2021)

A more complex example:

```
// The even numbers in the range of zero to nine.
let iter = (0..10).filter(|x| x % 2 == 0);

// We might iterate from zero to ten times. Knowing that it's five
// exactly wouldn't be possible without executing filter().
assert_eq!((0, Some(10)), iter.size_hint());

// Let's add five more numbers with chain()
let iter = (0..10).filter(|x| x % 2 == 0).chain(15..20);

// now both bounds are increased by five
assert_eq!((5, Some(15)), iter.size_hint());
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%2F%2F%20The%20even%20numbers%20in%20the%20range%20of%20zero%20to%20nine.%0Afn%20main()%20%7B%0Alet%20iter%20%3D%20(0..10).filter(%7Cx%7C%20x%20%25%202%20%3D%3D%200)%3B%0A%0A%2F%2F%20We%20might%20iterate%20from%20zero%20to%20ten%20times.%20Knowing%20that%20it%27s%20five%0A%2F%2F%20exactly%20wouldn%27t%20be%20possible%20without%20executing%20filter().%0Aassert_eq!((0%2C%20Some(10))%2C%20iter.size_hint())%3B%0A%0A%2F%2F%20Let%27s%20add%20five%20more%20numbers%20with%20chain()%0Alet%20iter%20%3D%20(0..10).filter(%7Cx%7C%20x%20%25%202%20%3D%3D%200).chain(15..20)%3B%0A%0A%2F%2F%20now%20both%20bounds%20are%20increased%20by%20five%0Aassert_eq!((5%2C%20Some(15))%2C%20iter.size_hint())%3B%0A%7D&edition=2021)

Returning `None` for an upper bound:

```
// an infinite iterator has no upper bound
// and the maximum possible lower bound
let iter = 0..;

assert_eq!((usize::MAX, None), iter.size_hint());
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%2F%2F%20an%20infinite%20iterator%20has%20no%20upper%20bound%0A%2F%2F%20and%20the%20maximum%20possible%20lower%20bound%0Afn%20main()%20%7B%0Alet%20iter%20%3D%200..%3B%0A%0Aassert_eq!((usize%3A%3AMAX%2C%20None)%2C%20iter.size_hint())%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#252-254)

#### fn [count](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.count)(self) -> [usize](https://doc.rust-lang.org/std/primitive.usize.html)

Consumes the iterator, counting the number of iterations and returning it.

This method will call [`next`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#tymethod.next) repeatedly until [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is encountered, returning the number of times it saw [`Some`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.Some "Some"). Note that [`next`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#tymethod.next) has to be called at least once even if the iterator does not have any elements.

##### [Overflow Behavior](https://doc.rust-lang.org/std/iter/trait.Iterator.html#overflow-behavior)

The method does no guarding against overflows, so counting elements of an iterator with more than [`usize::MAX`](https://doc.rust-lang.org/std/primitive.usize.html#associatedconstant.MAX "usize::MAX") elements either produces the wrong result or panics. If debug assertions are enabled, a panic is guaranteed.

##### [Panics](https://doc.rust-lang.org/std/iter/trait.Iterator.html#panics)

This function might panic if the iterator has more than [`usize::MAX`](https://doc.rust-lang.org/std/primitive.usize.html#associatedconstant.MAX "usize::MAX") elements.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-3)

Basic usage:

```
let a = [1, 2, 3];
assert_eq!(a.iter().count(), 3);

let a = [1, 2, 3, 4, 5];
assert_eq!(a.iter().count(), 5);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Aassert_eq!(a.iter().count()%2C%203)%3B%0A%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%2C%204%2C%205%5D%3B%0Aassert_eq!(a.iter().count()%2C%205)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#282-284)

#### fn [last](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.last)(self) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>

Consumes the iterator, returning the last element.

This method will evaluate the iterator until it returns [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None"). While doing so, it keeps track of the current element. After [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is returned, `last()` will then return the last element it saw.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-4)

Basic usage:

```
let a = [1, 2, 3];
assert_eq!(a.iter().last(), Some(&3));

let a = [1, 2, 3, 4, 5];
assert_eq!(a.iter().last(), Some(&5));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Aassert_eq!(a.iter().last()%2C%20Some(%263))%3B%0A%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%2C%204%2C%205%5D%3B%0Aassert_eq!(a.iter().last()%2C%20Some(%265))%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#328)

#### fn [advance\_by](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.advance_by)(&mut self, n: [usize](https://doc.rust-lang.org/std/primitive.usize.html)) -> [Result](https://doc.rust-lang.org/std/result/enum.Result.html "enum std::result::Result")<[()](https://doc.rust-lang.org/std/primitive.unit.html), [usize](https://doc.rust-lang.org/std/primitive.usize.html)\>

🔬 This is a nightly-only experimental API. (`iter_advance_by` [#77404](https://github.com/rust-lang/rust/issues/77404))

Advances the iterator by `n` elements.

This method will eagerly skip `n` elements by calling [`next`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#tymethod.next) up to `n` times until [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is encountered.

`advance_by(n)` will return [`Ok(())`](https://doc.rust-lang.org/std/result/enum.Result.html#variant.Ok "Ok") if the iterator successfully advances by `n` elements, or [`Err(k)`](https://doc.rust-lang.org/std/result/enum.Result.html#variant.Err "Err") if [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is encountered, where `k` is the number of elements the iterator is advanced by before running out of elements (i.e. the length of the iterator). Note that `k` is always less than `n`.

Calling `advance_by(0)` can do meaningful work, for example [`Flatten`](https://doc.rust-lang.org/std/iter/struct.Flatten.html) can advance its outer iterator until it finds an inner iterator that is not empty, which then often allows it to return a more accurate `size_hint()` than in its initial state.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-5)

Basic usage:

```
#![feature(iter_advance_by)]

let a = [1, 2, 3, 4];
let mut iter = a.iter();

assert_eq!(iter.advance_by(2), Ok(()));
assert_eq!(iter.next(), Some(&3));
assert_eq!(iter.advance_by(0), Ok(()));
assert_eq!(iter.advance_by(100), Err(1)); // only `&4` was skipped
```

[Run](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_advance_by)%5D%0A%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%2C%204%5D%3B%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0Aassert_eq!(iter.advance_by(2)%2C%20Ok(()))%3B%0Aassert_eq!(iter.next()%2C%20Some(%263))%3B%0Aassert_eq!(iter.advance_by(0)%2C%20Ok(()))%3B%0Aassert_eq!(iter.advance_by(100)%2C%20Err(1))%3B%20%2F%2F%20only%20%60%264%60%20was%20skipped%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#376)

#### fn [nth](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.nth)(&mut self, n: [usize](https://doc.rust-lang.org/std/primitive.usize.html)) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>

Returns the `n`th element of the iterator.

Like most indexing operations, the count starts from zero, so `nth(0)` returns the first value, `nth(1)` the second, and so on.

Note that all preceding elements, as well as the returned element, will be consumed from the iterator. That means that the preceding elements will be discarded, and also that calling `nth(0)` multiple times on the same iterator will return different elements.

`nth()` will return [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") if `n` is greater than or equal to the length of the iterator.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-6)

Basic usage:

```
let a = [1, 2, 3];
assert_eq!(a.iter().nth(1), Some(&2));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Aassert_eq!(a.iter().nth(1)%2C%20Some(%262))%3B%0A%7D&edition=2021)

Calling `nth()` multiple times doesn’t rewind the iterator:

```
let a = [1, 2, 3];

let mut iter = a.iter();

assert_eq!(iter.nth(1), Some(&2));
assert_eq!(iter.nth(1), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0Aassert_eq!(iter.nth(1)%2C%20Some(%262))%3B%0Aassert_eq!(iter.nth(1)%2C%20None)%3B%0A%7D&edition=2021)

Returning `None` if there are less than `n + 1` elements:

```
let a = [1, 2, 3];
assert_eq!(a.iter().nth(10), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Aassert_eq!(a.iter().nth(10)%2C%20None)%3B%0A%7D&edition=2021)

1.28.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#428-430)

#### fn [step\_by](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.step_by)(self, step: [usize](https://doc.rust-lang.org/std/primitive.usize.html)) -> [StepBy](https://doc.rust-lang.org/std/iter/struct.StepBy.html "struct std::iter::StepBy")<Self>ⓘ

Creates an iterator starting at the same point, but stepping by the given amount at each iteration.

Note 1: The first element of the iterator will always be returned, regardless of the step given.

Note 2: The time at which ignored elements are pulled is not fixed. `StepBy` behaves like the sequence `self.next()`, `self.nth(step-1)`, `self.nth(step-1)`, …, but is also free to behave like the sequence `advance_n_and_return_first(&mut self, step)`, `advance_n_and_return_first(&mut self, step)`, … Which way is used may change for some iterators for performance reasons. The second way will advance the iterator earlier and may consume more items.

`advance_n_and_return_first` is the equivalent of:

```
fn advance_n_and_return_first<I>(iter: &mut I, n: usize) -> Option<I::Item>
where
    I: Iterator,
{
    let next = iter.next();
    if n > 1 {
        iter.nth(n - 2);
    }
    next
}
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Afn%20advance_n_and_return_first%3CI%3E(iter%3A%20%26mut%20I%2C%20n%3A%20usize)%20-%3E%20Option%3CI%3A%3AItem%3E%0Awhere%0A%20%20%20%20I%3A%20Iterator%2C%0A%7B%0A%20%20%20%20let%20next%20%3D%20iter.next()%3B%0A%20%20%20%20if%20n%20%3E%201%20%7B%0A%20%20%20%20%20%20%20%20iter.nth(n%20-%202)%3B%0A%20%20%20%20%7D%0A%20%20%20%20next%0A%7D%0A%7D&edition=2021)

##### [Panics](https://doc.rust-lang.org/std/iter/trait.Iterator.html#panics-1)

The method will panic if the given step is `0`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-7)

Basic usage:

```
let a = [0, 1, 2, 3, 4, 5];
let mut iter = a.iter().step_by(2);

assert_eq!(iter.next(), Some(&0));
assert_eq!(iter.next(), Some(&2));
assert_eq!(iter.next(), Some(&4));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B0%2C%201%2C%202%2C%203%2C%204%2C%205%5D%3B%0Alet%20mut%20iter%20%3D%20a.iter().step_by(2)%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%260))%3B%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0Aassert_eq!(iter.next()%2C%20Some(%264))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#499-502)

#### fn [chain](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.chain)<U>(self, other: U) -> [Chain](https://doc.rust-lang.org/std/iter/struct.Chain.html "struct std::iter::Chain")<Self, <U as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[IntoIter](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.IntoIter "type std::iter::IntoIterator::IntoIter")\>ⓘwhere  
    U: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")<Item = Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>, 

Takes two iterators and creates a new iterator over both in sequence.

`chain()` will return a new iterator which will first iterate over values from the first iterator and then over values from the second iterator.

In other words, it links two iterators together, in a chain. 🔗

[`once`](https://doc.rust-lang.org/std/iter/fn.once.html) is commonly used to adapt a single value into a chain of other kinds of iteration.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-8)

Basic usage:

```
let a1 = [1, 2, 3];
let a2 = [4, 5, 6];

let mut iter = a1.iter().chain(a2.iter());

assert_eq!(iter.next(), Some(&1));
assert_eq!(iter.next(), Some(&2));
assert_eq!(iter.next(), Some(&3));
assert_eq!(iter.next(), Some(&4));
assert_eq!(iter.next(), Some(&5));
assert_eq!(iter.next(), Some(&6));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a1%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20a2%20%3D%20%5B4%2C%205%2C%206%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a1.iter().chain(a2.iter())%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0Aassert_eq!(iter.next()%2C%20Some(%263))%3B%0Aassert_eq!(iter.next()%2C%20Some(%264))%3B%0Aassert_eq!(iter.next()%2C%20Some(%265))%3B%0Aassert_eq!(iter.next()%2C%20Some(%266))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Since the argument to `chain()` uses [`IntoIterator`](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "IntoIterator"), we can pass anything that can be converted into an [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator"), not just an[`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") itself. For example, slices (`&[T]`) implement [`IntoIterator`](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "IntoIterator"), and so can be passed to `chain()` directly:

```
let s1 = &[1, 2, 3];
let s2 = &[4, 5, 6];

let mut iter = s1.iter().chain(s2);

assert_eq!(iter.next(), Some(&1));
assert_eq!(iter.next(), Some(&2));
assert_eq!(iter.next(), Some(&3));
assert_eq!(iter.next(), Some(&4));
assert_eq!(iter.next(), Some(&5));
assert_eq!(iter.next(), Some(&6));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20s1%20%3D%20%26%5B1%2C%202%2C%203%5D%3B%0Alet%20s2%20%3D%20%26%5B4%2C%205%2C%206%5D%3B%0A%0Alet%20mut%20iter%20%3D%20s1.iter().chain(s2)%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0Aassert_eq!(iter.next()%2C%20Some(%263))%3B%0Aassert_eq!(iter.next()%2C%20Some(%264))%3B%0Aassert_eq!(iter.next()%2C%20Some(%265))%3B%0Aassert_eq!(iter.next()%2C%20Some(%266))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

If you work with Windows API, you may wish to convert [`OsStr`](https://doc.rust-lang.org/std/ffi/struct.OsStr.html) to `Vec<u16>`:

```
#[cfg(windows)]
fn os_str_to_utf16(s: &std::ffi::OsStr) -> Vec<u16> {
    use std::os::windows::ffi::OsStrExt;
    s.encode_wide().chain(std::iter::once(0)).collect()
}
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0A%23%5Bcfg(windows)%5D%0Afn%20os_str_to_utf16(s%3A%20%26std%3A%3Affi%3A%3AOsStr)%20-%3E%20Vec%3Cu16%3E%20%7B%0A%20%20%20%20use%20std%3A%3Aos%3A%3Awindows%3A%3Affi%3A%3AOsStrExt%3B%0A%20%20%20%20s.encode_wide().chain(std%3A%3Aiter%3A%3Aonce(0)).collect()%0A%7D%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#617-620)

#### fn [zip](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.zip)<U>(self, other: U) -> [Zip](https://doc.rust-lang.org/std/iter/struct.Zip.html "struct std::iter::Zip")<Self, <U as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[IntoIter](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.IntoIter "type std::iter::IntoIterator::IntoIter")\>ⓘwhere  
    U: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"), 

‘Zips up’ two iterators into a single iterator of pairs.

`zip()` returns a new iterator that will iterate over two other iterators, returning a tuple where the first element comes from the first iterator, and the second element comes from the second iterator.

In other words, it zips two iterators together, into a single one.

If either iterator returns [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None"), [`next`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#tymethod.next) from the zipped iterator will return [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None"). If the zipped iterator has no more elements to return then each further attempt to advance it will first try to advance the first iterator at most one time and if it still yielded an item try to advance the second iterator at most one time.

To ‘undo’ the result of zipping up two iterators, see [`unzip`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.unzip).

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-9)

Basic usage:

```
let a1 = [1, 2, 3];
let a2 = [4, 5, 6];

let mut iter = a1.iter().zip(a2.iter());

assert_eq!(iter.next(), Some((&1, &4)));
assert_eq!(iter.next(), Some((&2, &5)));
assert_eq!(iter.next(), Some((&3, &6)));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a1%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20a2%20%3D%20%5B4%2C%205%2C%206%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a1.iter().zip(a2.iter())%3B%0A%0Aassert_eq!(iter.next()%2C%20Some((%261%2C%20%264)))%3B%0Aassert_eq!(iter.next()%2C%20Some((%262%2C%20%265)))%3B%0Aassert_eq!(iter.next()%2C%20Some((%263%2C%20%266)))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Since the argument to `zip()` uses [`IntoIterator`](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "IntoIterator"), we can pass anything that can be converted into an [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator"), not just an[`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") itself. For example, slices (`&[T]`) implement [`IntoIterator`](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "IntoIterator"), and so can be passed to `zip()` directly:

```
let s1 = &[1, 2, 3];
let s2 = &[4, 5, 6];

let mut iter = s1.iter().zip(s2);

assert_eq!(iter.next(), Some((&1, &4)));
assert_eq!(iter.next(), Some((&2, &5)));
assert_eq!(iter.next(), Some((&3, &6)));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20s1%20%3D%20%26%5B1%2C%202%2C%203%5D%3B%0Alet%20s2%20%3D%20%26%5B4%2C%205%2C%206%5D%3B%0A%0Alet%20mut%20iter%20%3D%20s1.iter().zip(s2)%3B%0A%0Aassert_eq!(iter.next()%2C%20Some((%261%2C%20%264)))%3B%0Aassert_eq!(iter.next()%2C%20Some((%262%2C%20%265)))%3B%0Aassert_eq!(iter.next()%2C%20Some((%263%2C%20%266)))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

`zip()` is often used to zip an infinite iterator to a finite one. This works because the finite iterator will eventually return [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None"), ending the zipper. Zipping with `(0..)` can look a lot like [`enumerate`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.enumerate):

```
let enumerate: Vec<_> = "foo".chars().enumerate().collect();

let zipper: Vec<_> = (0..).zip("foo".chars()).collect();

assert_eq!((0, 'f'), enumerate[0]);
assert_eq!((0, 'f'), zipper[0]);

assert_eq!((1, 'o'), enumerate[1]);
assert_eq!((1, 'o'), zipper[1]);

assert_eq!((2, 'o'), enumerate[2]);
assert_eq!((2, 'o'), zipper[2]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20enumerate%3A%20Vec%3C_%3E%20%3D%20%22foo%22.chars().enumerate().collect()%3B%0A%0Alet%20zipper%3A%20Vec%3C_%3E%20%3D%20(0..).zip(%22foo%22.chars()).collect()%3B%0A%0Aassert_eq!((0%2C%20%27f%27)%2C%20enumerate%5B0%5D)%3B%0Aassert_eq!((0%2C%20%27f%27)%2C%20zipper%5B0%5D)%3B%0A%0Aassert_eq!((1%2C%20%27o%27)%2C%20enumerate%5B1%5D)%3B%0Aassert_eq!((1%2C%20%27o%27)%2C%20zipper%5B1%5D)%3B%0A%0Aassert_eq!((2%2C%20%27o%27)%2C%20enumerate%5B2%5D)%3B%0Aassert_eq!((2%2C%20%27o%27)%2C%20zipper%5B2%5D)%3B%0A%7D&edition=2021)

If both iterators have roughly equivalent syntax, it may be more readable to use [`zip`](https://doc.rust-lang.org/std/iter/fn.zip.html):

```
use std::iter::zip;

let a = [1, 2, 3];
let b = [2, 3, 4];

let mut zipped = zip(
    a.into_iter().map(|x| x * 2).skip(1),
    b.into_iter().map(|x| x * 2).skip(1),
);

assert_eq!(zipped.next(), Some((4, 6)));
assert_eq!(zipped.next(), Some((6, 8)));
assert_eq!(zipped.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Ause%20std%3A%3Aiter%3A%3Azip%3B%0A%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20b%20%3D%20%5B2%2C%203%2C%204%5D%3B%0A%0Alet%20mut%20zipped%20%3D%20zip(%0A%20%20%20%20a.into_iter().map(%7Cx%7C%20x%20*%202).skip(1)%2C%0A%20%20%20%20b.into_iter().map(%7Cx%7C%20x%20*%202).skip(1)%2C%0A)%3B%0A%0Aassert_eq!(zipped.next()%2C%20Some((4%2C%206)))%3B%0Aassert_eq!(zipped.next()%2C%20Some((6%2C%208)))%3B%0Aassert_eq!(zipped.next()%2C%20None)%3B%0A%7D&edition=2021)

compared to:

```
let mut zipped = a
    .into_iter()
    .map(|x| x * 2)
    .skip(1)
    .zip(b.into_iter().map(|x| x * 2).skip(1));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20b%20%3D%20%5B2%2C%203%2C%204%5D%3B%0A%0Alet%20mut%20zipped%20%3D%20a%0A%20%20%20%20.into_iter()%0A%20%20%20%20.map(%7Cx%7C%20x%20*%202)%0A%20%20%20%20.skip(1)%0A%20%20%20%20.zip(b.into_iter().map(%7Cx%7C%20x%20*%202).skip(1))%3B%0A%0Aassert_eq!(zipped.next()%2C%20Some((4%2C%206)))%3B%0Aassert_eq!(zipped.next()%2C%20Some((6%2C%208)))%3B%0Aassert_eq!(zipped.next()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#659-662)

#### fn [intersperse](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.intersperse)(self, separator: Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [Intersperse](https://doc.rust-lang.org/std/iter/struct.Intersperse.html "struct std::iter::Intersperse")<Self>ⓘwhere  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [Clone](https://doc.rust-lang.org/std/clone/trait.Clone.html "trait std::clone::Clone"), 

🔬 This is a nightly-only experimental API. (`iter_intersperse` [#79524](https://github.com/rust-lang/rust/issues/79524))

Creates a new iterator which places a copy of `separator` between adjacent items of the original iterator.

In case `separator` does not implement [`Clone`](https://doc.rust-lang.org/std/clone/trait.Clone.html) or needs to be computed every time, use [`intersperse_with`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.intersperse_with).

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-10)

Basic usage:

```
#![feature(iter_intersperse)]

let mut a = [0, 1, 2].iter().intersperse(&100);
assert_eq!(a.next(), Some(&0));   // The first element from `a`.
assert_eq!(a.next(), Some(&100)); // The separator.
assert_eq!(a.next(), Some(&1));   // The next element from `a`.
assert_eq!(a.next(), Some(&100)); // The separator.
assert_eq!(a.next(), Some(&2));   // The last element from `a`.
assert_eq!(a.next(), None);       // The iterator is finished.
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_intersperse)%5D%0A%0Afn%20main()%20%7B%0Alet%20mut%20a%20%3D%20%5B0%2C%201%2C%202%5D.iter().intersperse(%26100)%3B%0Aassert_eq!(a.next()%2C%20Some(%260))%3B%20%20%20%2F%2F%20The%20first%20element%20from%20%60a%60.%0Aassert_eq!(a.next()%2C%20Some(%26100))%3B%20%2F%2F%20The%20separator.%0Aassert_eq!(a.next()%2C%20Some(%261))%3B%20%20%20%2F%2F%20The%20next%20element%20from%20%60a%60.%0Aassert_eq!(a.next()%2C%20Some(%26100))%3B%20%2F%2F%20The%20separator.%0Aassert_eq!(a.next()%2C%20Some(%262))%3B%20%20%20%2F%2F%20The%20last%20element%20from%20%60a%60.%0Aassert_eq!(a.next()%2C%20None)%3B%20%20%20%20%20%20%20%2F%2F%20The%20iterator%20is%20finished.%0A%7D&version=nightly&edition=2021)

`intersperse` can be very useful to join an iterator’s items using a common element:

```
#![feature(iter_intersperse)]

let hello = ["Hello", "World", "!"].iter().copied().intersperse(" ").collect::<String>();
assert_eq!(hello, "Hello World !");
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_intersperse)%5D%0A%0Afn%20main()%20%7B%0Alet%20hello%20%3D%20%5B%22Hello%22%2C%20%22World%22%2C%20%22!%22%5D.iter().copied().intersperse(%22%20%22).collect%3A%3A%3CString%3E()%3B%0Aassert_eq!(hello%2C%20%22Hello%20World%20!%22)%3B%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#717-720)

#### fn [intersperse\_with](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.intersperse_with)<G>(self, separator: G) -> [IntersperseWith](https://doc.rust-lang.org/std/iter/struct.IntersperseWith.html "struct std::iter::IntersperseWith")<Self, G>ⓘwhere  
    G: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")() -> Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), 

🔬 This is a nightly-only experimental API. (`iter_intersperse` [#79524](https://github.com/rust-lang/rust/issues/79524))

Creates a new iterator which places an item generated by `separator` between adjacent items of the original iterator.

The closure will be called exactly once each time an item is placed between two adjacent items from the underlying iterator; specifically, the closure is not called if the underlying iterator yields less than two items and after the last item is yielded.

If the iterator’s item implements [`Clone`](https://doc.rust-lang.org/std/clone/trait.Clone.html), it may be easier to use [`intersperse`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.intersperse).

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-11)

Basic usage:

```
#![feature(iter_intersperse)]

#[derive(PartialEq, Debug)]
struct NotClone(usize);

let v = [NotClone(0), NotClone(1), NotClone(2)];
let mut it = v.into_iter().intersperse_with(|| NotClone(99));

assert_eq!(it.next(), Some(NotClone(0)));  // The first element from `v`.
assert_eq!(it.next(), Some(NotClone(99))); // The separator.
assert_eq!(it.next(), Some(NotClone(1)));  // The next element from `v`.
assert_eq!(it.next(), Some(NotClone(99))); // The separator.
assert_eq!(it.next(), Some(NotClone(2)));  // The last element from from `v`.
assert_eq!(it.next(), None);               // The iterator is finished.
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_intersperse)%5D%0A%0Afn%20main()%20%7B%0A%23%5Bderive(PartialEq%2C%20Debug)%5D%0Astruct%20NotClone(usize)%3B%0A%0Alet%20v%20%3D%20%5BNotClone(0)%2C%20NotClone(1)%2C%20NotClone(2)%5D%3B%0Alet%20mut%20it%20%3D%20v.into_iter().intersperse_with(%7C%7C%20NotClone(99))%3B%0A%0Aassert_eq!(it.next()%2C%20Some(NotClone(0)))%3B%20%20%2F%2F%20The%20first%20element%20from%20%60v%60.%0Aassert_eq!(it.next()%2C%20Some(NotClone(99)))%3B%20%2F%2F%20The%20separator.%0Aassert_eq!(it.next()%2C%20Some(NotClone(1)))%3B%20%20%2F%2F%20The%20next%20element%20from%20%60v%60.%0Aassert_eq!(it.next()%2C%20Some(NotClone(99)))%3B%20%2F%2F%20The%20separator.%0Aassert_eq!(it.next()%2C%20Some(NotClone(2)))%3B%20%20%2F%2F%20The%20last%20element%20from%20from%20%60v%60.%0Aassert_eq!(it.next()%2C%20None)%3B%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%2F%2F%20The%20iterator%20is%20finished.%0A%7D&version=nightly&edition=2021)

`intersperse_with` can be used in situations where the separator needs to be computed:

```
#![feature(iter_intersperse)]

let src = ["Hello", "to", "all", "people", "!!"].iter().copied();

// The closure mutably borrows its context to generate an item.
let mut happy_emojis = [" ❤️ ", " 😀 "].iter().copied();
let separator = || happy_emojis.next().unwrap_or(" 🦀 ");

let result = src.intersperse_with(separator).collect::<String>();
assert_eq!(result, "Hello ❤️ to 😀 all 🦀 people 🦀 !!");
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_intersperse)%5D%0A%0Afn%20main()%20%7B%0Alet%20src%20%3D%20%5B%22Hello%22%2C%20%22to%22%2C%20%22all%22%2C%20%22people%22%2C%20%22!!%22%5D.iter().copied()%3B%0A%0A%2F%2F%20The%20closure%20mutably%20borrows%20its%20context%20to%20generate%20an%20item.%0Alet%20mut%20happy_emojis%20%3D%20%5B%22%20%E2%9D%A4%EF%B8%8F%20%22%2C%20%22%20%F0%9F%98%80%20%22%5D.iter().copied()%3B%0Alet%20separator%20%3D%20%7C%7C%20happy_emojis.next().unwrap_or(%22%20%F0%9F%A6%80%20%22)%3B%0A%0Alet%20result%20%3D%20src.intersperse_with(separator).collect%3A%3A%3CString%3E()%3B%0Aassert_eq!(result%2C%20%22Hello%20%E2%9D%A4%EF%B8%8F%20to%20%F0%9F%98%80%20all%20%F0%9F%A6%80%20people%20%F0%9F%A6%80%20!!%22)%3B%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#776-779)

#### fn [map](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.map)<B, F>(self, f: F) -> [Map](https://doc.rust-lang.org/std/iter/struct.Map.html "struct std::iter::Map")<Self, F>ⓘwhere  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> B, 

Takes a closure and creates an iterator which calls that closure on each element.

`map()` transforms one iterator into another, by means of its argument: something that implements [`FnMut`](https://doc.rust-lang.org/std/ops/trait.FnMut.html). It produces a new iterator which calls this closure on each element of the original iterator.

If you are good at thinking in types, you can think of `map()` like this: If you have an iterator that gives you elements of some type `A`, and you want an iterator of some other type `B`, you can use `map()`, passing a closure that takes an `A` and returns a `B`.

`map()` is conceptually similar to a [`for`](https://doc.rust-lang.org/book/ch03-05-control-flow.html#looping-through-a-collection-with-for) loop. However, as `map()` is lazy, it is best used when you’re already working with other iterators. If you’re doing some sort of looping for a side effect, it’s considered more idiomatic to use [`for`](https://doc.rust-lang.org/book/ch03-05-control-flow.html#looping-through-a-collection-with-for) than `map()`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-12)

Basic usage:

```
let a = [1, 2, 3];

let mut iter = a.iter().map(|x| 2 * x);

assert_eq!(iter.next(), Some(2));
assert_eq!(iter.next(), Some(4));
assert_eq!(iter.next(), Some(6));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().map(%7Cx%7C%202%20*%20x)%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(2))%3B%0Aassert_eq!(iter.next()%2C%20Some(4))%3B%0Aassert_eq!(iter.next()%2C%20Some(6))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

If you’re doing some sort of side effect, prefer [`for`](https://doc.rust-lang.org/book/ch03-05-control-flow.html#looping-through-a-collection-with-for) to `map()`:

```
// don't do this:
(0..5).map(|x| println!("{x}"));

// it won't even execute, as it is lazy. Rust will warn you about this.

// Instead, use for:
for x in 0..5 {
    println!("{x}");
}
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Ballow(unused_must_use)%5D%0A%2F%2F%20don%27t%20do%20this%3A%0Afn%20main()%20%7B%0A(0..5).map(%7Cx%7C%20println!(%22%7Bx%7D%22))%3B%0A%0A%2F%2F%20it%20won%27t%20even%20execute%2C%20as%20it%20is%20lazy.%20Rust%20will%20warn%20you%20about%20this.%0A%0A%2F%2F%20Instead%2C%20use%20for%3A%0Afor%20x%20in%200..5%20%7B%0A%20%20%20%20println!(%22%7Bx%7D%22)%3B%0A%7D%0A%7D&edition=2021)

1.21.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#821-824)

#### fn [for\_each](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.for_each)<F>(self, f: F) where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")), 

Calls a closure on each element of an iterator.

This is equivalent to using a [`for`](https://doc.rust-lang.org/book/ch03-05-control-flow.html#looping-through-a-collection-with-for) loop on the iterator, although `break` and `continue` are not possible from a closure. It’s generally more idiomatic to use a `for` loop, but `for_each` may be more legible when processing items at the end of longer iterator chains. In some cases `for_each` may also be faster than a loop, because it will use internal iteration on adapters like `Chain`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-13)

Basic usage:

```
use std::sync::mpsc::channel;

let (tx, rx) = channel();
(0..5).map(|x| x * 2 + 1)
      .for_each(move |x| tx.send(x).unwrap());

let v: Vec<_> =  rx.iter().collect();
assert_eq!(v, vec![1, 3, 5, 7, 9]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Ause%20std%3A%3Async%3A%3Ampsc%3A%3Achannel%3B%0A%0Alet%20(tx%2C%20rx)%20%3D%20channel()%3B%0A(0..5).map(%7Cx%7C%20x%20*%202%20%2B%201)%0A%20%20%20%20%20%20.for_each(move%20%7Cx%7C%20tx.send(x).unwrap())%3B%0A%0Alet%20v%3A%20Vec%3C_%3E%20%3D%20%20rx.iter().collect()%3B%0Aassert_eq!(v%2C%20vec!%5B1%2C%203%2C%205%2C%207%2C%209%5D)%3B%0A%7D&edition=2021)

For such a small example, a `for` loop may be cleaner, but `for_each` might be preferable to keep a functional style with longer iterators:

```
(0..5).flat_map(|x| x * 100 .. x * 110)
      .enumerate()
      .filter(|&(i, x)| (i + x) % 3 == 0)
      .for_each(|(i, x)| println!("{i}:{x}"));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0A(0..5).flat_map(%7Cx%7C%20x%20*%20100%20..%20x%20*%20110)%0A%20%20%20%20%20%20.enumerate()%0A%20%20%20%20%20%20.filter(%7C%26(i%2C%20x)%7C%20(i%20%2B%20x)%20%25%203%20%3D%3D%200)%0A%20%20%20%20%20%20.for_each(%7C(i%2C%20x)%7C%20println!(%22%7Bi%7D%3A%7Bx%7D%22))%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#896-899)

#### fn [filter](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.filter)<P>(self, predicate: P) -> [Filter](https://doc.rust-lang.org/std/iter/struct.Filter.html "struct std::iter::Filter")<Self, P>ⓘwhere  
    P: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

Creates an iterator which uses a closure to determine if an element should be yielded.

Given an element the closure must return `true` or `false`. The returned iterator will yield only the elements for which the closure returns true.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-14)

Basic usage:

```
let a = [0i32, 1, 2];

let mut iter = a.iter().filter(|x| x.is_positive());

assert_eq!(iter.next(), Some(&1));
assert_eq!(iter.next(), Some(&2));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B0i32%2C%201%2C%202%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().filter(%7Cx%7C%20x.is_positive())%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Because the closure passed to `filter()` takes a reference, and many iterators iterate over references, this leads to a possibly confusing situation, where the type of the closure is a double reference:

```
let a = [0, 1, 2];

let mut iter = a.iter().filter(|x| **x > 1); // need two *s!

assert_eq!(iter.next(), Some(&2));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B0%2C%201%2C%202%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().filter(%7Cx%7C%20**x%20%3E%201)%3B%20%2F%2F%20need%20two%20*s!%0A%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

It’s common to instead use destructuring on the argument to strip away one:

```
let a = [0, 1, 2];

let mut iter = a.iter().filter(|&x| *x > 1); // both & and *

assert_eq!(iter.next(), Some(&2));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B0%2C%201%2C%202%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().filter(%7C%26x%7C%20*x%20%3E%201)%3B%20%2F%2F%20both%20%26%20and%20*%0A%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

or both:

```
let a = [0, 1, 2];

let mut iter = a.iter().filter(|&&x| x > 1); // two &s

assert_eq!(iter.next(), Some(&2));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B0%2C%201%2C%202%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().filter(%7C%26%26x%7C%20x%20%3E%201)%3B%20%2F%2F%20two%20%26s%0A%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

of these layers.

Note that `iter.filter(f).next()` is equivalent to `iter.find(f)`.

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#941-944)

#### fn [filter\_map](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.filter_map)<B, F>(self, f: F) -> [FilterMap](https://doc.rust-lang.org/std/iter/struct.FilterMap.html "struct std::iter::FilterMap")<Self, F>ⓘwhere  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<B>, 

Creates an iterator that both filters and maps.

The returned iterator yields only the `value`s for which the supplied closure returns `Some(value)`.

`filter_map` can be used to make chains of [`filter`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.filter) and [`map`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.map) more concise. The example below shows how a `map().filter().map()` can be shortened to a single call to `filter_map`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-15)

Basic usage:

```
let a = ["1", "two", "NaN", "four", "5"];

let mut iter = a.iter().filter_map(|s| s.parse().ok());

assert_eq!(iter.next(), Some(1));
assert_eq!(iter.next(), Some(5));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B%221%22%2C%20%22two%22%2C%20%22NaN%22%2C%20%22four%22%2C%20%225%22%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().filter_map(%7Cs%7C%20s.parse().ok())%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(1))%3B%0Aassert_eq!(iter.next()%2C%20Some(5))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Here’s the same example, but with [`filter`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.filter) and [`map`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.map):

```
let a = ["1", "two", "NaN", "four", "5"];
let mut iter = a.iter().map(|s| s.parse()).filter(|s| s.is_ok()).map(|s| s.unwrap());
assert_eq!(iter.next(), Some(1));
assert_eq!(iter.next(), Some(5));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B%221%22%2C%20%22two%22%2C%20%22NaN%22%2C%20%22four%22%2C%20%225%22%5D%3B%0Alet%20mut%20iter%20%3D%20a.iter().map(%7Cs%7C%20s.parse()).filter(%7Cs%7C%20s.is_ok()).map(%7Cs%7C%20s.unwrap())%3B%0Aassert_eq!(iter.next()%2C%20Some(1))%3B%0Aassert_eq!(iter.next()%2C%20Some(5))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#987-989)

#### fn [enumerate](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.enumerate)(self) -> [Enumerate](https://doc.rust-lang.org/std/iter/struct.Enumerate.html "struct std::iter::Enumerate")<Self>ⓘ

Creates an iterator which gives the current iteration count as well as the next value.

The iterator returned yields pairs `(i, val)`, where `i` is the current index of iteration and `val` is the value returned by the iterator.

`enumerate()` keeps its count as a [`usize`](https://doc.rust-lang.org/std/primitive.usize.html "usize"). If you want to count by a different sized integer, the [`zip`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.zip) function provides similar functionality.

##### [Overflow Behavior](https://doc.rust-lang.org/std/iter/trait.Iterator.html#overflow-behavior-1)

The method does no guarding against overflows, so enumerating more than [`usize::MAX`](https://doc.rust-lang.org/std/primitive.usize.html#associatedconstant.MAX "usize::MAX") elements either produces the wrong result or panics. If debug assertions are enabled, a panic is guaranteed.

##### [Panics](https://doc.rust-lang.org/std/iter/trait.Iterator.html#panics-2)

The returned iterator might panic if the to-be-returned index would overflow a [`usize`](https://doc.rust-lang.org/std/primitive.usize.html "usize").

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-16)

```
let a = ['a', 'b', 'c'];

let mut iter = a.iter().enumerate();

assert_eq!(iter.next(), Some((0, &'a')));
assert_eq!(iter.next(), Some((1, &'b')));
assert_eq!(iter.next(), Some((2, &'c')));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B%27a%27%2C%20%27b%27%2C%20%27c%27%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().enumerate()%3B%0A%0Aassert_eq!(iter.next()%2C%20Some((0%2C%20%26%27a%27)))%3B%0Aassert_eq!(iter.next()%2C%20Some((1%2C%20%26%27b%27)))%3B%0Aassert_eq!(iter.next()%2C%20Some((2%2C%20%26%27c%27)))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1058-1060)

#### fn [peekable](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.peekable)(self) -> [Peekable](https://doc.rust-lang.org/std/iter/struct.Peekable.html "struct std::iter::Peekable")<Self>ⓘ

Creates an iterator which can use the [`peek`](https://doc.rust-lang.org/std/iter/struct.Peekable.html#method.peek) and [`peek_mut`](https://doc.rust-lang.org/std/iter/struct.Peekable.html#method.peek_mut) methods to look at the next element of the iterator without consuming it. See their documentation for more information.

Note that the underlying iterator is still advanced when [`peek`](https://doc.rust-lang.org/std/iter/struct.Peekable.html#method.peek) or [`peek_mut`](https://doc.rust-lang.org/std/iter/struct.Peekable.html#method.peek_mut) are called for the first time: In order to retrieve the next element, [`next`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#tymethod.next) is called on the underlying iterator, hence any side effects (i.e. anything other than fetching the next value) of the [`next`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#tymethod.next) method will occur.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-17)

Basic usage:

```
let xs = [1, 2, 3];

let mut iter = xs.iter().peekable();

// peek() lets us see into the future
assert_eq!(iter.peek(), Some(&&1));
assert_eq!(iter.next(), Some(&1));

assert_eq!(iter.next(), Some(&2));

// we can peek() multiple times, the iterator won't advance
assert_eq!(iter.peek(), Some(&&3));
assert_eq!(iter.peek(), Some(&&3));

assert_eq!(iter.next(), Some(&3));

// after the iterator is finished, so is peek()
assert_eq!(iter.peek(), None);
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20xs%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20xs.iter().peekable()%3B%0A%0A%2F%2F%20peek()%20lets%20us%20see%20into%20the%20future%0Aassert_eq!(iter.peek()%2C%20Some(%26%261))%3B%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0A%0A%2F%2F%20we%20can%20peek()%20multiple%20times%2C%20the%20iterator%20won%27t%20advance%0Aassert_eq!(iter.peek()%2C%20Some(%26%263))%3B%0Aassert_eq!(iter.peek()%2C%20Some(%26%263))%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%263))%3B%0A%0A%2F%2F%20after%20the%20iterator%20is%20finished%2C%20so%20is%20peek()%0Aassert_eq!(iter.peek()%2C%20None)%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Using [`peek_mut`](https://doc.rust-lang.org/std/iter/struct.Peekable.html#method.peek_mut) to mutate the next item without advancing the iterator:

```
let xs = [1, 2, 3];

let mut iter = xs.iter().peekable();

// `peek_mut()` lets us see into the future
assert_eq!(iter.peek_mut(), Some(&mut &1));
assert_eq!(iter.peek_mut(), Some(&mut &1));
assert_eq!(iter.next(), Some(&1));

if let Some(mut p) = iter.peek_mut() {
    assert_eq!(*p, &2);
    // put a value into the iterator
    *p = &1000;
}

// The value reappears as the iterator continues
assert_eq!(iter.collect::<Vec<_>>(), vec![&1000, &3]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20xs%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20xs.iter().peekable()%3B%0A%0A%2F%2F%20%60peek_mut()%60%20lets%20us%20see%20into%20the%20future%0Aassert_eq!(iter.peek_mut()%2C%20Some(%26mut%20%261))%3B%0Aassert_eq!(iter.peek_mut()%2C%20Some(%26mut%20%261))%3B%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0A%0Aif%20let%20Some(mut%20p)%20%3D%20iter.peek_mut()%20%7B%0A%20%20%20%20assert_eq!(*p%2C%20%262)%3B%0A%20%20%20%20%2F%2F%20put%20a%20value%20into%20the%20iterator%0A%20%20%20%20*p%20%3D%20%261000%3B%0A%7D%0A%0A%2F%2F%20The%20value%20reappears%20as%20the%20iterator%20continues%0Aassert_eq!(iter.collect%3A%3A%3CVec%3C_%3E%3E()%2C%20vec!%5B%261000%2C%20%263%5D)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1123-1126)

#### fn [skip\_while](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.skip_while)<P>(self, predicate: P) -> [SkipWhile](https://doc.rust-lang.org/std/iter/struct.SkipWhile.html "struct std::iter::SkipWhile")<Self, P>ⓘwhere  
    P: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

Creates an iterator that [`skip`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.skip)s elements based on a predicate.

`skip_while()` takes a closure as an argument. It will call this closure on each element of the iterator, and ignore elements until it returns `false`.

After `false` is returned, `skip_while()`’s job is over, and the rest of the elements are yielded.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-18)

Basic usage:

```
let a = [-1i32, 0, 1];

let mut iter = a.iter().skip_while(|x| x.is_negative());

assert_eq!(iter.next(), Some(&0));
assert_eq!(iter.next(), Some(&1));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-1i32%2C%200%2C%201%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().skip_while(%7Cx%7C%20x.is_negative())%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%260))%3B%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Because the closure passed to `skip_while()` takes a reference, and many iterators iterate over references, this leads to a possibly confusing situation, where the type of the closure argument is a double reference:

```
let a = [-1, 0, 1];

let mut iter = a.iter().skip_while(|x| **x < 0); // need two *s!

assert_eq!(iter.next(), Some(&0));
assert_eq!(iter.next(), Some(&1));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-1%2C%200%2C%201%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().skip_while(%7Cx%7C%20**x%20%3C%200)%3B%20%2F%2F%20need%20two%20*s!%0A%0Aassert_eq!(iter.next()%2C%20Some(%260))%3B%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Stopping after an initial `false`:

```
let a = [-1, 0, 1, -2];

let mut iter = a.iter().skip_while(|x| **x < 0);

assert_eq!(iter.next(), Some(&0));
assert_eq!(iter.next(), Some(&1));

// while this would have been false, since we already got a false,
// skip_while() isn't used any more
assert_eq!(iter.next(), Some(&-2));

assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-1%2C%200%2C%201%2C%20-2%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().skip_while(%7Cx%7C%20**x%20%3C%200)%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%260))%3B%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0A%0A%2F%2F%20while%20this%20would%20have%20been%20false%2C%20since%20we%20already%20got%20a%20false%2C%0A%2F%2F%20skip_while()%20isn%27t%20used%20any%20more%0Aassert_eq!(iter.next()%2C%20Some(%26-2))%3B%0A%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1204-1207)

#### fn [take\_while](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.take_while)<P>(self, predicate: P) -> [TakeWhile](https://doc.rust-lang.org/std/iter/struct.TakeWhile.html "struct std::iter::TakeWhile")<Self, P>ⓘwhere  
    P: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

Creates an iterator that yields elements based on a predicate.

`take_while()` takes a closure as an argument. It will call this closure on each element of the iterator, and yield elements while it returns `true`.

After `false` is returned, `take_while()`’s job is over, and the rest of the elements are ignored.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-19)

Basic usage:

```
let a = [-1i32, 0, 1];

let mut iter = a.iter().take_while(|x| x.is_negative());

assert_eq!(iter.next(), Some(&-1));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-1i32%2C%200%2C%201%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().take_while(%7Cx%7C%20x.is_negative())%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%26-1))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Because the closure passed to `take_while()` takes a reference, and many iterators iterate over references, this leads to a possibly confusing situation, where the type of the closure is a double reference:

```
let a = [-1, 0, 1];

let mut iter = a.iter().take_while(|x| **x < 0); // need two *s!

assert_eq!(iter.next(), Some(&-1));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-1%2C%200%2C%201%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().take_while(%7Cx%7C%20**x%20%3C%200)%3B%20%2F%2F%20need%20two%20*s!%0A%0Aassert_eq!(iter.next()%2C%20Some(%26-1))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Stopping after an initial `false`:

```
let a = [-1, 0, 1, -2];

let mut iter = a.iter().take_while(|x| **x < 0);

assert_eq!(iter.next(), Some(&-1));

// We have more elements that are less than zero, but since we already
// got a false, take_while() isn't used any more
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-1%2C%200%2C%201%2C%20-2%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().take_while(%7Cx%7C%20**x%20%3C%200)%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%26-1))%3B%0A%0A%2F%2F%20We%20have%20more%20elements%20that%20are%20less%20than%20zero%2C%20but%20since%20we%20already%0A%2F%2F%20got%20a%20false%2C%20take_while()%20isn%27t%20used%20any%20more%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Because `take_while()` needs to look at the value in order to see if it should be included or not, consuming iterators will see that it is removed:

```
let a = [1, 2, 3, 4];
let mut iter = a.iter();

let result: Vec<i32> = iter.by_ref()
                           .take_while(|n| **n != 3)
                           .cloned()
                           .collect();

assert_eq!(result, &[1, 2]);

let result: Vec<i32> = iter.cloned().collect();

assert_eq!(result, &[4]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%2C%204%5D%3B%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0Alet%20result%3A%20Vec%3Ci32%3E%20%3D%20iter.by_ref()%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.take_while(%7Cn%7C%20**n%20!%3D%203)%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.cloned()%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.collect()%3B%0A%0Aassert_eq!(result%2C%20%26%5B1%2C%202%5D)%3B%0A%0Alet%20result%3A%20Vec%3Ci32%3E%20%3D%20iter.cloned().collect()%3B%0A%0Aassert_eq!(result%2C%20%26%5B4%5D)%3B%0A%7D&edition=2021)

The `3` is no longer there, because it was consumed in order to see if the iteration should stop, but wasn’t placed back into the iterator.

1.57.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1292-1295)

#### fn [map\_while](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.map_while)<B, P>(self, predicate: P) -> [MapWhile](https://doc.rust-lang.org/std/iter/struct.MapWhile.html "struct std::iter::MapWhile")<Self, P>ⓘwhere  
    P: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<B>, 

Creates an iterator that both yields elements based on a predicate and maps.

`map_while()` takes a closure as an argument. It will call this closure on each element of the iterator, and yield elements while it returns [`Some(_)`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.Some "Some").

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-20)

Basic usage:

```
let a = [-1i32, 4, 0, 1];

let mut iter = a.iter().map_while(|x| 16i32.checked_div(*x));

assert_eq!(iter.next(), Some(-16));
assert_eq!(iter.next(), Some(4));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-1i32%2C%204%2C%200%2C%201%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().map_while(%7Cx%7C%2016i32.checked_div(*x))%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(-16))%3B%0Aassert_eq!(iter.next()%2C%20Some(4))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Here’s the same example, but with [`take_while`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.take_while) and [`map`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.map):

```
let a = [-1i32, 4, 0, 1];

let mut iter = a.iter()
                .map(|x| 16i32.checked_div(*x))
                .take_while(|x| x.is_some())
                .map(|x| x.unwrap());

assert_eq!(iter.next(), Some(-16));
assert_eq!(iter.next(), Some(4));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-1i32%2C%204%2C%200%2C%201%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter()%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.map(%7Cx%7C%2016i32.checked_div(*x))%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.take_while(%7Cx%7C%20x.is_some())%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.map(%7Cx%7C%20x.unwrap())%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(-16))%3B%0Aassert_eq!(iter.next()%2C%20Some(4))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

Stopping after an initial [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None"):

```
let a = [0, 1, 2, -3, 4, 5, -6];

let iter = a.iter().map_while(|x| u32::try_from(*x).ok());
let vec = iter.collect::<Vec<_>>();

// We have more elements which could fit in u32 (4, 5), but `map_while` returned `None` for `-3`
// (as the `predicate` returned `None`) and `collect` stops at the first `None` encountered.
assert_eq!(vec, vec![0, 1, 2]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B0%2C%201%2C%202%2C%20-3%2C%204%2C%205%2C%20-6%5D%3B%0A%0Alet%20iter%20%3D%20a.iter().map_while(%7Cx%7C%20u32%3A%3Atry_from(*x).ok())%3B%0Alet%20vec%20%3D%20iter.collect%3A%3A%3CVec%3C_%3E%3E()%3B%0A%0A%2F%2F%20We%20have%20more%20elements%20which%20could%20fit%20in%20u32%20(4%2C%205)%2C%20but%20%60map_while%60%20returned%20%60None%60%20for%20%60-3%60%0A%2F%2F%20(as%20the%20%60predicate%60%20returned%20%60None%60)%20and%20%60collect%60%20stops%20at%20the%20first%20%60None%60%20encountered.%0Aassert_eq!(vec%2C%20vec!%5B0%2C%201%2C%202%5D)%3B%0A%7D&edition=2021)

Because `map_while()` needs to look at the value in order to see if it should be included or not, consuming iterators will see that it is removed:

```
let a = [1, 2, -3, 4];
let mut iter = a.iter();

let result: Vec<u32> = iter.by_ref()
                           .map_while(|n| u32::try_from(*n).ok())
                           .collect();

assert_eq!(result, &[1, 2]);

let result: Vec<i32> = iter.cloned().collect();

assert_eq!(result, &[4]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%20-3%2C%204%5D%3B%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0Alet%20result%3A%20Vec%3Cu32%3E%20%3D%20iter.by_ref()%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.map_while(%7Cn%7C%20u32%3A%3Atry_from(*n).ok())%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.collect()%3B%0A%0Aassert_eq!(result%2C%20%26%5B1%2C%202%5D)%3B%0A%0Alet%20result%3A%20Vec%3Ci32%3E%20%3D%20iter.cloned().collect()%3B%0A%0Aassert_eq!(result%2C%20%26%5B4%5D)%3B%0A%7D&edition=2021)

The `-3` is no longer there, because it was consumed in order to see if the iteration should stop, but wasn’t placed back into the iterator.

Note that unlike [`take_while`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.take_while) this iterator is **not** fused. It is also not specified what this iterator returns after the first [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is returned. If you need fused iterator, use [`fuse`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.fuse).

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1323-1325)

#### fn [skip](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.skip)(self, n: [usize](https://doc.rust-lang.org/std/primitive.usize.html)) -> [Skip](https://doc.rust-lang.org/std/iter/struct.Skip.html "struct std::iter::Skip")<Self>ⓘ

Creates an iterator that skips the first `n` elements.

`skip(n)` skips elements until `n` elements are skipped or the end of the iterator is reached (whichever happens first). After that, all the remaining elements are yielded. In particular, if the original iterator is too short, then the returned iterator is empty.

Rather than overriding this method directly, instead override the `nth` method.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-21)

Basic usage:

```
let a = [1, 2, 3];

let mut iter = a.iter().skip(2);

assert_eq!(iter.next(), Some(&3));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().skip(2)%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%263))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1376-1378)

#### fn [take](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.take)(self, n: [usize](https://doc.rust-lang.org/std/primitive.usize.html)) -> [Take](https://doc.rust-lang.org/std/iter/struct.Take.html "struct std::iter::Take")<Self>ⓘ

Creates an iterator that yields the first `n` elements, or fewer if the underlying iterator ends sooner.

`take(n)` yields elements until `n` elements are yielded or the end of the iterator is reached (whichever happens first). The returned iterator is a prefix of length `n` if the original iterator contains at least `n` elements, otherwise it contains all of the (fewer than `n`) elements of the original iterator.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-22)

Basic usage:

```
let a = [1, 2, 3];

let mut iter = a.iter().take(2);

assert_eq!(iter.next(), Some(&1));
assert_eq!(iter.next(), Some(&2));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().take(2)%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

`take()` is often used with an infinite iterator, to make it finite:

```
let mut iter = (0..).take(3);

assert_eq!(iter.next(), Some(0));
assert_eq!(iter.next(), Some(1));
assert_eq!(iter.next(), Some(2));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20mut%20iter%20%3D%20(0..).take(3)%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(0))%3B%0Aassert_eq!(iter.next()%2C%20Some(1))%3B%0Aassert_eq!(iter.next()%2C%20Some(2))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

If less than `n` elements are available, `take` will limit itself to the size of the underlying iterator:

```
let v = [1, 2];
let mut iter = v.into_iter().take(5);
assert_eq!(iter.next(), Some(1));
assert_eq!(iter.next(), Some(2));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20v%20%3D%20%5B1%2C%202%5D%3B%0Alet%20mut%20iter%20%3D%20v.into_iter().take(5)%3B%0Aassert_eq!(iter.next()%2C%20Some(1))%3B%0Aassert_eq!(iter.next()%2C%20Some(2))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1420-1423)

#### fn [scan](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.scan)<St, B, F>(self, initial\_state: St, f: F) -> [Scan](https://doc.rust-lang.org/std/iter/struct.Scan.html "struct std::iter::Scan")<Self, St, F>ⓘwhere  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")([&mut](https://doc.rust-lang.org/std/primitive.reference.html) St, Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<B>, 

An iterator adapter similar to [`fold`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.fold) that holds internal state and produces a new iterator.

`scan()` takes two arguments: an initial value which seeds the internal state, and a closure with two arguments, the first being a mutable reference to the internal state and the second an iterator element. The closure can assign to the internal state to share state between iterations.

On iteration, the closure will be applied to each element of the iterator and the return value from the closure, an [`Option`](https://doc.rust-lang.org/std/option/enum.Option.html "Option"), is yielded by the iterator.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-23)

Basic usage:

```
let a = [1, 2, 3];

let mut iter = a.iter().scan(1, |state, &x| {
    // each iteration, we'll multiply the state by the element
    *state = *state * x;

    // then, we'll yield the negation of the state
    Some(-*state)
});

assert_eq!(iter.next(), Some(-1));
assert_eq!(iter.next(), Some(-2));
assert_eq!(iter.next(), Some(-6));
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().scan(1%2C%20%7Cstate%2C%20%26x%7C%20%7B%0A%20%20%20%20%2F%2F%20each%20iteration%2C%20we%27ll%20multiply%20the%20state%20by%20the%20element%0A%20%20%20%20*state%20%3D%20*state%20*%20x%3B%0A%0A%20%20%20%20%2F%2F%20then%2C%20we%27ll%20yield%20the%20negation%20of%20the%20state%0A%20%20%20%20Some(-*state)%0A%7D)%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(-1))%3B%0Aassert_eq!(iter.next()%2C%20Some(-2))%3B%0Aassert_eq!(iter.next()%2C%20Some(-6))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1460-1464)

#### fn [flat\_map](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.flat_map)<U, F>(self, f: F) -> [FlatMap](https://doc.rust-lang.org/std/iter/struct.FlatMap.html "struct std::iter::FlatMap")<Self, U, F>ⓘwhere  
    U: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> U, 

Creates an iterator that works like map, but flattens nested structure.

The [`map`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.map) adapter is very useful, but only when the closure argument produces values. If it produces an iterator instead, there’s an extra layer of indirection. `flat_map()` will remove this extra layer on its own.

You can think of `flat_map(f)` as the semantic equivalent of [`map`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.map)ping, and then [`flatten`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.flatten)ing as in `map(f).flatten()`.

Another way of thinking about `flat_map()`: [`map`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.map)’s closure returns one item for each element, and `flat_map()`’s closure returns an iterator for each element.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-24)

Basic usage:

```
let words = ["alpha", "beta", "gamma"];

// chars() returns an iterator
let merged: String = words.iter()
                          .flat_map(|s| s.chars())
                          .collect();
assert_eq!(merged, "alphabetagamma");
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20words%20%3D%20%5B%22alpha%22%2C%20%22beta%22%2C%20%22gamma%22%5D%3B%0A%0A%2F%2F%20chars()%20returns%20an%20iterator%0Alet%20merged%3A%20String%20%3D%20words.iter()%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.flat_map(%7Cs%7C%20s.chars())%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.collect()%3B%0Aassert_eq!(merged%2C%20%22alphabetagamma%22)%3B%0A%7D&edition=2021)

1.29.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1532-1535)

#### fn [flatten](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.flatten)(self) -> [Flatten](https://doc.rust-lang.org/std/iter/struct.Flatten.html "struct std::iter::Flatten")<Self>ⓘwhere  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"), 

Creates an iterator that flattens nested structure.

This is useful when you have an iterator of iterators or an iterator of things that can be turned into iterators and you want to remove one level of indirection.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-25)

Basic usage:

```
let data = vec![vec![1, 2, 3, 4], vec![5, 6]];
let flattened = data.into_iter().flatten().collect::<Vec<u8>>();
assert_eq!(flattened, &[1, 2, 3, 4, 5, 6]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20data%20%3D%20vec!%5Bvec!%5B1%2C%202%2C%203%2C%204%5D%2C%20vec!%5B5%2C%206%5D%5D%3B%0Alet%20flattened%20%3D%20data.into_iter().flatten().collect%3A%3A%3CVec%3Cu8%3E%3E()%3B%0Aassert_eq!(flattened%2C%20%26%5B1%2C%202%2C%203%2C%204%2C%205%2C%206%5D)%3B%0A%7D&edition=2021)

Mapping and then flattening:

```
let words = ["alpha", "beta", "gamma"];

// chars() returns an iterator
let merged: String = words.iter()
                          .map(|s| s.chars())
                          .flatten()
                          .collect();
assert_eq!(merged, "alphabetagamma");
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20words%20%3D%20%5B%22alpha%22%2C%20%22beta%22%2C%20%22gamma%22%5D%3B%0A%0A%2F%2F%20chars()%20returns%20an%20iterator%0Alet%20merged%3A%20String%20%3D%20words.iter()%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.map(%7Cs%7C%20s.chars())%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.flatten()%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.collect()%3B%0Aassert_eq!(merged%2C%20%22alphabetagamma%22)%3B%0A%7D&edition=2021)

You can also rewrite this in terms of [`flat_map()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.flat_map), which is preferable in this case since it conveys intent more clearly:

```
let words = ["alpha", "beta", "gamma"];

// chars() returns an iterator
let merged: String = words.iter()
                          .flat_map(|s| s.chars())
                          .collect();
assert_eq!(merged, "alphabetagamma");
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20words%20%3D%20%5B%22alpha%22%2C%20%22beta%22%2C%20%22gamma%22%5D%3B%0A%0A%2F%2F%20chars()%20returns%20an%20iterator%0Alet%20merged%3A%20String%20%3D%20words.iter()%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.flat_map(%7Cs%7C%20s.chars())%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.collect()%3B%0Aassert_eq!(merged%2C%20%22alphabetagamma%22)%3B%0A%7D&edition=2021)

Flattening only removes one level of nesting at a time:

```
let d3 = [[[1, 2], [3, 4]], [[5, 6], [7, 8]]];

let d2 = d3.iter().flatten().collect::<Vec<_>>();
assert_eq!(d2, [&[1, 2], &[3, 4], &[5, 6], &[7, 8]]);

let d1 = d3.iter().flatten().flatten().collect::<Vec<_>>();
assert_eq!(d1, [&1, &2, &3, &4, &5, &6, &7, &8]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20d3%20%3D%20%5B%5B%5B1%2C%202%5D%2C%20%5B3%2C%204%5D%5D%2C%20%5B%5B5%2C%206%5D%2C%20%5B7%2C%208%5D%5D%5D%3B%0A%0Alet%20d2%20%3D%20d3.iter().flatten().collect%3A%3A%3CVec%3C_%3E%3E()%3B%0Aassert_eq!(d2%2C%20%5B%26%5B1%2C%202%5D%2C%20%26%5B3%2C%204%5D%2C%20%26%5B5%2C%206%5D%2C%20%26%5B7%2C%208%5D%5D)%3B%0A%0Alet%20d1%20%3D%20d3.iter().flatten().flatten().collect%3A%3A%3CVec%3C_%3E%3E()%3B%0Aassert_eq!(d1%2C%20%5B%261%2C%20%262%2C%20%263%2C%20%264%2C%20%265%2C%20%266%2C%20%267%2C%20%268%5D)%3B%0A%7D&edition=2021)

Here we see that `flatten()` does not perform a “deep” flatten. Instead, only one level of nesting is removed. That is, if you `flatten()` a three-dimensional array, the result will be two-dimensional and not one-dimensional. To get a one-dimensional structure, you have to `flatten()` again.

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1600-1602)

#### fn [fuse](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.fuse)(self) -> [Fuse](https://doc.rust-lang.org/std/iter/struct.Fuse.html "struct std::iter::Fuse")<Self>ⓘ

Creates an iterator which ends after the first [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None").

After an iterator returns [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None"), future calls may or may not yield [`Some(T)`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.Some) again. `fuse()` adapts an iterator, ensuring that after a[`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is given, it will always return [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") forever.

Note that the [`Fuse`](https://doc.rust-lang.org/std/iter/struct.Fuse.html "Fuse") wrapper is a no-op on iterators that implement the [`FusedIterator`](https://doc.rust-lang.org/std/iter/trait.FusedIterator.html) trait. `fuse()` may therefore behave incorrectly if the [`FusedIterator`](https://doc.rust-lang.org/std/iter/trait.FusedIterator.html) trait is improperly implemented.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-26)

Basic usage:

```
// an iterator which alternates between Some and None
struct Alternate {
    state: i32,
}

impl Iterator for Alternate {
    type Item = i32;

    fn next(&mut self) -> Option<i32> {
        let val = self.state;
        self.state = self.state + 1;

        // if it's even, Some(i32), else None
        if val % 2 == 0 {
            Some(val)
        } else {
            None
        }
    }
}

let mut iter = Alternate { state: 0 };

// we can see our iterator going back and forth
assert_eq!(iter.next(), Some(0));
assert_eq!(iter.next(), None);
assert_eq!(iter.next(), Some(2));
assert_eq!(iter.next(), None);

// however, once we fuse it...
let mut iter = iter.fuse();

assert_eq!(iter.next(), Some(4));
assert_eq!(iter.next(), None);

// it will always return `None` after the first time.
assert_eq!(iter.next(), None);
assert_eq!(iter.next(), None);
assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%2F%2F%20an%20iterator%20which%20alternates%20between%20Some%20and%20None%0Afn%20main()%20%7B%0Astruct%20Alternate%20%7B%0A%20%20%20%20state%3A%20i32%2C%0A%7D%0A%0Aimpl%20Iterator%20for%20Alternate%20%7B%0A%20%20%20%20type%20Item%20%3D%20i32%3B%0A%0A%20%20%20%20fn%20next(%26mut%20self)%20-%3E%20Option%3Ci32%3E%20%7B%0A%20%20%20%20%20%20%20%20let%20val%20%3D%20self.state%3B%0A%20%20%20%20%20%20%20%20self.state%20%3D%20self.state%20%2B%201%3B%0A%0A%20%20%20%20%20%20%20%20%2F%2F%20if%20it%27s%20even%2C%20Some(i32)%2C%20else%20None%0A%20%20%20%20%20%20%20%20if%20val%20%25%202%20%3D%3D%200%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20Some(val)%0A%20%20%20%20%20%20%20%20%7D%20else%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20None%0A%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%7D%0A%7D%0A%0Alet%20mut%20iter%20%3D%20Alternate%20%7B%20state%3A%200%20%7D%3B%0A%0A%2F%2F%20we%20can%20see%20our%20iterator%20going%20back%20and%20forth%0Aassert_eq!(iter.next()%2C%20Some(0))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0Aassert_eq!(iter.next()%2C%20Some(2))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%0A%2F%2F%20however%2C%20once%20we%20fuse%20it...%0Alet%20mut%20iter%20%3D%20iter.fuse()%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(4))%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%0A%2F%2F%20it%20will%20always%20return%20%60None%60%20after%20the%20first%20time.%0Aassert_eq!(iter.next()%2C%20None)%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1684-1687)

#### fn [inspect](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.inspect)<F>(self, f: F) -> [Inspect](https://doc.rust-lang.org/std/iter/struct.Inspect.html "struct std::iter::Inspect")<Self, F>ⓘwhere  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")), 

Does something with each element of an iterator, passing the value on.

When using iterators, you’ll often chain several of them together. While working on such code, you might want to check out what’s happening at various parts in the pipeline. To do that, insert a call to `inspect()`.

It’s more common for `inspect()` to be used as a debugging tool than to exist in your final code, but applications may find it useful in certain situations when errors need to be logged before being discarded.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-27)

Basic usage:

```
let a = [1, 4, 2, 3];

// this iterator sequence is complex.
let sum = a.iter()
    .cloned()
    .filter(|x| x % 2 == 0)
    .fold(0, |sum, i| sum + i);

println!("{sum}");

// let's add some inspect() calls to investigate what's happening
let sum = a.iter()
    .cloned()
    .inspect(|x| println!("about to filter: {x}"))
    .filter(|x| x % 2 == 0)
    .inspect(|x| println!("made it through filter: {x}"))
    .fold(0, |sum, i| sum + i);

println!("{sum}");
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%204%2C%202%2C%203%5D%3B%0A%0A%2F%2F%20this%20iterator%20sequence%20is%20complex.%0Alet%20sum%20%3D%20a.iter()%0A%20%20%20%20.cloned()%0A%20%20%20%20.filter(%7Cx%7C%20x%20%25%202%20%3D%3D%200)%0A%20%20%20%20.fold(0%2C%20%7Csum%2C%20i%7C%20sum%20%2B%20i)%3B%0A%0Aprintln!(%22%7Bsum%7D%22)%3B%0A%0A%2F%2F%20let%27s%20add%20some%20inspect()%20calls%20to%20investigate%20what%27s%20happening%0Alet%20sum%20%3D%20a.iter()%0A%20%20%20%20.cloned()%0A%20%20%20%20.inspect(%7Cx%7C%20println!(%22about%20to%20filter%3A%20%7Bx%7D%22))%0A%20%20%20%20.filter(%7Cx%7C%20x%20%25%202%20%3D%3D%200)%0A%20%20%20%20.inspect(%7Cx%7C%20println!(%22made%20it%20through%20filter%3A%20%7Bx%7D%22))%0A%20%20%20%20.fold(0%2C%20%7Csum%2C%20i%7C%20sum%20%2B%20i)%3B%0A%0Aprintln!(%22%7Bsum%7D%22)%3B%0A%7D&edition=2021)

This will print:

```
6
about to filter: 1
about to filter: 4
made it through filter: 4
about to filter: 2
made it through filter: 2
about to filter: 3
6
```

Logging errors before discarding them:

```
let lines = ["1", "2", "a"];

let sum: i32 = lines
    .iter()
    .map(|line| line.parse::<i32>())
    .inspect(|num| {
        if let Err(ref e) = *num {
            println!("Parsing error: {e}");
        }
    })
    .filter_map(Result::ok)
    .sum();

println!("Sum: {sum}");
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20lines%20%3D%20%5B%221%22%2C%20%222%22%2C%20%22a%22%5D%3B%0A%0Alet%20sum%3A%20i32%20%3D%20lines%0A%20%20%20%20.iter()%0A%20%20%20%20.map(%7Cline%7C%20line.parse%3A%3A%3Ci32%3E())%0A%20%20%20%20.inspect(%7Cnum%7C%20%7B%0A%20%20%20%20%20%20%20%20if%20let%20Err(ref%20e)%20%3D%20*num%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20println!(%22Parsing%20error%3A%20%7Be%7D%22)%3B%0A%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%7D)%0A%20%20%20%20.filter_map(Result%3A%3Aok)%0A%20%20%20%20.sum()%3B%0A%0Aprintln!(%22Sum%3A%20%7Bsum%7D%22)%3B%0A%7D&edition=2021)

This will print:

```
Parsing error: invalid digit found in string
Sum: 3
```

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1714-1716)

#### fn [by\_ref](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.by_ref)(&mut self) -> [&mut](https://doc.rust-lang.org/std/primitive.reference.html) Self

Borrows an iterator, rather than consuming it.

This is useful to allow applying iterator adapters while still retaining ownership of the original iterator.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-28)

Basic usage:

```
let mut words = ["hello", "world", "of", "Rust"].into_iter();

// Take the first two words.
let hello_world: Vec<_> = words.by_ref().take(2).collect();
assert_eq!(hello_world, vec!["hello", "world"]);

// Collect the rest of the words.
// We can only do this because we used `by_ref` earlier.
let of_rust: Vec<_> = words.collect();
assert_eq!(of_rust, vec!["of", "Rust"]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20mut%20words%20%3D%20%5B%22hello%22%2C%20%22world%22%2C%20%22of%22%2C%20%22Rust%22%5D.into_iter()%3B%0A%0A%2F%2F%20Take%20the%20first%20two%20words.%0Alet%20hello_world%3A%20Vec%3C_%3E%20%3D%20words.by_ref().take(2).collect()%3B%0Aassert_eq!(hello_world%2C%20vec!%5B%22hello%22%2C%20%22world%22%5D)%3B%0A%0A%2F%2F%20Collect%20the%20rest%20of%20the%20words.%0A%2F%2F%20We%20can%20only%20do%20this%20because%20we%20used%20%60by_ref%60%20earlier.%0Alet%20of_rust%3A%20Vec%3C_%3E%20%3D%20words.collect()%3B%0Aassert_eq!(of_rust%2C%20vec!%5B%22of%22%2C%20%22Rust%22%5D)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1832-1834)

#### fn [collect](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.collect)<B>(self) -> B where  
    B: [FromIterator](https://doc.rust-lang.org/std/iter/trait.FromIterator.html "trait std::iter::FromIterator")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>, 

Transforms an iterator into a collection.

`collect()` can take anything iterable, and turn it into a relevant collection. This is one of the more powerful methods in the standard library, used in a variety of contexts.

The most basic pattern in which `collect()` is used is to turn one collection into another. You take a collection, call [`iter`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#tymethod.next) on it, do a bunch of transformations, and then `collect()` at the end.

`collect()` can also create instances of types that are not typical collections. For example, a [`String`](https://doc.rust-lang.org/std/string/struct.String.html) can be built from [`char`](https://doc.rust-lang.org/std/primitive.char.html)s, and an iterator of [`Result<T, E>`](https://doc.rust-lang.org/std/result/enum.Result.html "Result") items can be collected into `Result<Collection<T>, E>`. See the examples below for more.

Because `collect()` is so general, it can cause problems with type inference. As such, `collect()` is one of the few times you’ll see the syntax affectionately known as the ‘turbofish’: `::<>`. This helps the inference algorithm understand specifically which collection you’re trying to collect into.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-29)

Basic usage:

```
let a = [1, 2, 3];

let doubled: Vec<i32> = a.iter()
                         .map(|&x| x * 2)
                         .collect();

assert_eq!(vec![2, 4, 6], doubled);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20doubled%3A%20Vec%3Ci32%3E%20%3D%20a.iter()%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.map(%7C%26x%7C%20x%20*%202)%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20.collect()%3B%0A%0Aassert_eq!(vec!%5B2%2C%204%2C%206%5D%2C%20doubled)%3B%0A%7D&edition=2021)

Note that we needed the `: Vec<i32>` on the left-hand side. This is because we could collect into, for example, a [`VecDeque<T>`](https://doc.rust-lang.org/std/collections/struct.VecDeque.html)instead:

```
use std::collections::VecDeque;

let a = [1, 2, 3];

let doubled: VecDeque<i32> = a.iter().map(|&x| x * 2).collect();

assert_eq!(2, doubled[0]);
assert_eq!(4, doubled[1]);
assert_eq!(6, doubled[2]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Ause%20std%3A%3Acollections%3A%3AVecDeque%3B%0A%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20doubled%3A%20VecDeque%3Ci32%3E%20%3D%20a.iter().map(%7C%26x%7C%20x%20*%202).collect()%3B%0A%0Aassert_eq!(2%2C%20doubled%5B0%5D)%3B%0Aassert_eq!(4%2C%20doubled%5B1%5D)%3B%0Aassert_eq!(6%2C%20doubled%5B2%5D)%3B%0A%7D&edition=2021)

Using the ‘turbofish’ instead of annotating `doubled`:

```
let a = [1, 2, 3];

let doubled = a.iter().map(|x| x * 2).collect::<Vec<i32>>();

assert_eq!(vec![2, 4, 6], doubled);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20doubled%20%3D%20a.iter().map(%7Cx%7C%20x%20*%202).collect%3A%3A%3CVec%3Ci32%3E%3E()%3B%0A%0Aassert_eq!(vec!%5B2%2C%204%2C%206%5D%2C%20doubled)%3B%0A%7D&edition=2021)

Because `collect()` only cares about what you’re collecting into, you can still use a partial type hint, `_`, with the turbofish:

```
let a = [1, 2, 3];

let doubled = a.iter().map(|x| x * 2).collect::<Vec<_>>();

assert_eq!(vec![2, 4, 6], doubled);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20doubled%20%3D%20a.iter().map(%7Cx%7C%20x%20*%202).collect%3A%3A%3CVec%3C_%3E%3E()%3B%0A%0Aassert_eq!(vec!%5B2%2C%204%2C%206%5D%2C%20doubled)%3B%0A%7D&edition=2021)

Using `collect()` to make a [`String`](https://doc.rust-lang.org/std/string/struct.String.html):

```
let chars = ['g', 'd', 'k', 'k', 'n'];

let hello: String = chars.iter()
    .map(|&x| x as u8)
    .map(|x| (x + 1) as char)
    .collect();

assert_eq!("hello", hello);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20chars%20%3D%20%5B%27g%27%2C%20%27d%27%2C%20%27k%27%2C%20%27k%27%2C%20%27n%27%5D%3B%0A%0Alet%20hello%3A%20String%20%3D%20chars.iter()%0A%20%20%20%20.map(%7C%26x%7C%20x%20as%20u8)%0A%20%20%20%20.map(%7Cx%7C%20(x%20%2B%201)%20as%20char)%0A%20%20%20%20.collect()%3B%0A%0Aassert_eq!(%22hello%22%2C%20hello)%3B%0A%7D&edition=2021)

If you have a list of [`Result<T, E>`](https://doc.rust-lang.org/std/result/enum.Result.html "Result")s, you can use `collect()` to see if any of them failed:

```
let results = [Ok(1), Err("nope"), Ok(3), Err("bad")];

let result: Result<Vec<_>, &str> = results.iter().cloned().collect();

// gives us the first error
assert_eq!(Err("nope"), result);

let results = [Ok(1), Ok(3)];

let result: Result<Vec<_>, &str> = results.iter().cloned().collect();

// gives us the list of answers
assert_eq!(Ok(vec![1, 3]), result);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20results%20%3D%20%5BOk(1)%2C%20Err(%22nope%22)%2C%20Ok(3)%2C%20Err(%22bad%22)%5D%3B%0A%0Alet%20result%3A%20Result%3CVec%3C_%3E%2C%20%26str%3E%20%3D%20results.iter().cloned().collect()%3B%0A%0A%2F%2F%20gives%20us%20the%20first%20error%0Aassert_eq!(Err(%22nope%22)%2C%20result)%3B%0A%0Alet%20results%20%3D%20%5BOk(1)%2C%20Ok(3)%5D%3B%0A%0Alet%20result%3A%20Result%3CVec%3C_%3E%2C%20%26str%3E%20%3D%20results.iter().cloned().collect()%3B%0A%0A%2F%2F%20gives%20us%20the%20list%20of%20answers%0Aassert_eq!(Ok(vec!%5B1%2C%203%5D)%2C%20result)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1910-1915)

#### fn [try\_collect](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.try_collect)<B>(  
    &mut self  
) -> <<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item") as [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")\>::[Residual](https://doc.rust-lang.org/std/ops/trait.Try.html#associatedtype.Residual "type std::ops::Try::Residual") as [Residual](https://doc.rust-lang.org/std/ops/trait.Residual.html "trait std::ops::Residual")<B>>::[TryType](https://doc.rust-lang.org/std/ops/trait.Residual.html#associatedtype.TryType "type std::ops::Residual::TryType")where  
    B: [FromIterator](https://doc.rust-lang.org/std/iter/trait.FromIterator.html "trait std::iter::FromIterator")<<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item") as [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")\>::[Output](https://doc.rust-lang.org/std/ops/trait.Try.html#associatedtype.Output "type std::ops::Try::Output")\>,  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try"),  
    <Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item") as [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")\>::[Residual](https://doc.rust-lang.org/std/ops/trait.Try.html#associatedtype.Residual "type std::ops::Try::Residual"): [Residual](https://doc.rust-lang.org/std/ops/trait.Residual.html "trait std::ops::Residual")<B>, 

🔬 This is a nightly-only experimental API. (`iterator_try_collect` [#94047](https://github.com/rust-lang/rust/issues/94047))

Fallibly transforms an iterator into a collection, short circuiting if a failure is encountered.

`try_collect()` is a variation of [`collect()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.collect) that allows fallible conversions during collection. Its main use case is simplifying conversions from iterators yielding [`Option<T>`](https://doc.rust-lang.org/std/option/enum.Option.html "Option") into `Option<Collection<T>>`, or similarly for other [`Try`](https://doc.rust-lang.org/std/ops/trait.Try.html "Try") types (e.g. [`Result`](https://doc.rust-lang.org/std/result/enum.Result.html "Result")).

Importantly, `try_collect()` doesn’t require that the outer [`Try`](https://doc.rust-lang.org/std/ops/trait.Try.html "Try") type also implements [`FromIterator`](https://doc.rust-lang.org/std/iter/trait.FromIterator.html "FromIterator"); only the inner type produced on `Try::Output` must implement it. Concretely, this means that collecting into `ControlFlow<_, Vec<i32>>` is valid because `Vec<i32>` implements [`FromIterator`](https://doc.rust-lang.org/std/iter/trait.FromIterator.html "FromIterator"), even though [`ControlFlow`](https://doc.rust-lang.org/std/ops/enum.ControlFlow.html "ControlFlow") doesn’t.

Also, if a failure is encountered during `try_collect()`, the iterator is still valid and may continue to be used, in which case it will continue iterating starting after the element that triggered the failure. See the last example below for an example of how this works.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-30)

Successfully collecting an iterator of `Option<i32>` into `Option<Vec<i32>>`:

```
#![feature(iterator_try_collect)]

let u = vec![Some(1), Some(2), Some(3)];
let v = u.into_iter().try_collect::<Vec<i32>>();
assert_eq!(v, Some(vec![1, 2, 3]));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iterator_try_collect)%5D%0A%0Afn%20main()%20%7B%0Alet%20u%20%3D%20vec!%5BSome(1)%2C%20Some(2)%2C%20Some(3)%5D%3B%0Alet%20v%20%3D%20u.into_iter().try_collect%3A%3A%3CVec%3Ci32%3E%3E()%3B%0Aassert_eq!(v%2C%20Some(vec!%5B1%2C%202%2C%203%5D))%3B%0A%7D&version=nightly&edition=2021)

Failing to collect in the same way:

```
#![feature(iterator_try_collect)]

let u = vec![Some(1), Some(2), None, Some(3)];
let v = u.into_iter().try_collect::<Vec<i32>>();
assert_eq!(v, None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iterator_try_collect)%5D%0A%0Afn%20main()%20%7B%0Alet%20u%20%3D%20vec!%5BSome(1)%2C%20Some(2)%2C%20None%2C%20Some(3)%5D%3B%0Alet%20v%20%3D%20u.into_iter().try_collect%3A%3A%3CVec%3Ci32%3E%3E()%3B%0Aassert_eq!(v%2C%20None)%3B%0A%7D&version=nightly&edition=2021)

A similar example, but with `Result`:

```
#![feature(iterator_try_collect)]

let u: Vec<Result<i32, ()>> = vec![Ok(1), Ok(2), Ok(3)];
let v = u.into_iter().try_collect::<Vec<i32>>();
assert_eq!(v, Ok(vec![1, 2, 3]));

let u = vec![Ok(1), Ok(2), Err(()), Ok(3)];
let v = u.into_iter().try_collect::<Vec<i32>>();
assert_eq!(v, Err(()));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iterator_try_collect)%5D%0A%0Afn%20main()%20%7B%0Alet%20u%3A%20Vec%3CResult%3Ci32%2C%20()%3E%3E%20%3D%20vec!%5BOk(1)%2C%20Ok(2)%2C%20Ok(3)%5D%3B%0Alet%20v%20%3D%20u.into_iter().try_collect%3A%3A%3CVec%3Ci32%3E%3E()%3B%0Aassert_eq!(v%2C%20Ok(vec!%5B1%2C%202%2C%203%5D))%3B%0A%0Alet%20u%20%3D%20vec!%5BOk(1)%2C%20Ok(2)%2C%20Err(())%2C%20Ok(3)%5D%3B%0Alet%20v%20%3D%20u.into_iter().try_collect%3A%3A%3CVec%3Ci32%3E%3E()%3B%0Aassert_eq!(v%2C%20Err(()))%3B%0A%7D&version=nightly&edition=2021)

Finally, even [`ControlFlow`](https://doc.rust-lang.org/std/ops/enum.ControlFlow.html "ControlFlow") works, despite the fact that it doesn’t implement [`FromIterator`](https://doc.rust-lang.org/std/iter/trait.FromIterator.html "FromIterator"). Note also that the iterator can continue to be used, even if a failure is encountered:

```
#![feature(iterator_try_collect)]

use core::ops::ControlFlow::{Break, Continue};

let u = [Continue(1), Continue(2), Break(3), Continue(4), Continue(5)];
let mut it = u.into_iter();

let v = it.try_collect::<Vec<_>>();
assert_eq!(v, Break(3));

let v = it.try_collect::<Vec<_>>();
assert_eq!(v, Continue(vec![4, 5]));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iterator_try_collect)%5D%0A%0Afn%20main()%20%7B%0Ause%20core%3A%3Aops%3A%3AControlFlow%3A%3A%7BBreak%2C%20Continue%7D%3B%0A%0Alet%20u%20%3D%20%5BContinue(1)%2C%20Continue(2)%2C%20Break(3)%2C%20Continue(4)%2C%20Continue(5)%5D%3B%0Alet%20mut%20it%20%3D%20u.into_iter()%3B%0A%0Alet%20v%20%3D%20it.try_collect%3A%3A%3CVec%3C_%3E%3E()%3B%0Aassert_eq!(v%2C%20Break(3))%3B%0A%0Alet%20v%20%3D%20it.try_collect%3A%3A%3CVec%3C_%3E%3E()%3B%0Aassert_eq!(v%2C%20Continue(vec!%5B4%2C%205%5D))%3B%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#1983-1985)

#### fn [collect\_into](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.collect_into)<E>(self, collection: [&mut](https://doc.rust-lang.org/std/primitive.reference.html) E) -> [&mut](https://doc.rust-lang.org/std/primitive.reference.html) E where  
    E: [Extend](https://doc.rust-lang.org/std/iter/trait.Extend.html "trait std::iter::Extend")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>, 

🔬 This is a nightly-only experimental API. (`iter_collect_into` [#94780](https://github.com/rust-lang/rust/issues/94780))

Collects all the items from an iterator into a collection.

This method consumes the iterator and adds all its items to the passed collection. The collection is then returned, so the call chain can be continued.

This is useful when you already have a collection and wants to add the iterator items to it.

This method is a convenience method to call [Extend::extend](https://doc.rust-lang.org/std/iter/trait.Extend.html), but instead of being called on a collection, it’s called on an iterator.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-31)

Basic usage:

```
#![feature(iter_collect_into)]

let a = [1, 2, 3];
let mut vec: Vec::<i32> = vec![0, 1];

a.iter().map(|&x| x * 2).collect_into(&mut vec);
a.iter().map(|&x| x * 10).collect_into(&mut vec);

assert_eq!(vec![0, 1, 2, 4, 6, 10, 20, 30], vec);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_collect_into)%5D%0A%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20mut%20vec%3A%20Vec%3A%3A%3Ci32%3E%20%3D%20vec!%5B0%2C%201%5D%3B%0A%0Aa.iter().map(%7C%26x%7C%20x%20*%202).collect_into(%26mut%20vec)%3B%0Aa.iter().map(%7C%26x%7C%20x%20*%2010).collect_into(%26mut%20vec)%3B%0A%0Aassert_eq!(vec!%5B0%2C%201%2C%202%2C%204%2C%206%2C%2010%2C%2020%2C%2030%5D%2C%20vec)%3B%0A%7D&version=nightly&edition=2021)

`Vec` can have a manual set capacity to avoid reallocating it:

```
#![feature(iter_collect_into)]

let a = [1, 2, 3];
let mut vec: Vec::<i32> = Vec::with_capacity(6);

a.iter().map(|&x| x * 2).collect_into(&mut vec);
a.iter().map(|&x| x * 10).collect_into(&mut vec);

assert_eq!(6, vec.capacity());
println!("{:?}", vec);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_collect_into)%5D%0A%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20mut%20vec%3A%20Vec%3A%3A%3Ci32%3E%20%3D%20Vec%3A%3Awith_capacity(6)%3B%0A%0Aa.iter().map(%7C%26x%7C%20x%20*%202).collect_into(%26mut%20vec)%3B%0Aa.iter().map(%7C%26x%7C%20x%20*%2010).collect_into(%26mut%20vec)%3B%0A%0Aassert_eq!(6%2C%20vec.capacity())%3B%0Aprintln!(%22%7B%3A%3F%7D%22%2C%20vec)%3B%0A%7D&version=nightly&edition=2021)

The returned mutable reference can be used to continue the call chain:

```
#![feature(iter_collect_into)]

let a = [1, 2, 3];
let mut vec: Vec::<i32> = Vec::with_capacity(6);

let count = a.iter().collect_into(&mut vec).iter().count();

assert_eq!(count, vec.len());
println!("Vec len is {}", count);

let count = a.iter().collect_into(&mut vec).iter().count();

assert_eq!(count, vec.len());
println!("Vec len now is {}", count);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_collect_into)%5D%0A%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20mut%20vec%3A%20Vec%3A%3A%3Ci32%3E%20%3D%20Vec%3A%3Awith_capacity(6)%3B%0A%0Alet%20count%20%3D%20a.iter().collect_into(%26mut%20vec).iter().count()%3B%0A%0Aassert_eq!(count%2C%20vec.len())%3B%0Aprintln!(%22Vec%20len%20is%20%7B%7D%22%2C%20count)%3B%0A%0Alet%20count%20%3D%20a.iter().collect_into(%26mut%20vec).iter().count()%3B%0A%0Aassert_eq!(count%2C%20vec.len())%3B%0Aprintln!(%22Vec%20len%20now%20is%20%7B%7D%22%2C%20count)%3B%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2017-2021)

#### fn [partition](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.partition)<B, F>(self, f: F) -> [(B, B)](https://doc.rust-lang.org/std/primitive.tuple.html)where  
    B: [Default](https://doc.rust-lang.org/std/default/trait.Default.html "trait std::default::Default") + [Extend](https://doc.rust-lang.org/std/iter/trait.Extend.html "trait std::iter::Extend")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>,  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

Consumes an iterator, creating two collections from it.

The predicate passed to `partition()` can return `true`, or `false`. `partition()` returns a pair, all of the elements for which it returned `true`, and all of the elements for which it returned `false`.

See also [`is_partitioned()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.is_partitioned) and [`partition_in_place()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.partition_in_place).

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-32)

Basic usage:

```
let a = [1, 2, 3];

let (even, odd): (Vec<_>, Vec<_>) = a
    .into_iter()
    .partition(|n| n % 2 == 0);

assert_eq!(even, vec![2]);
assert_eq!(odd, vec![1, 3]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20(even%2C%20odd)%3A%20(Vec%3C_%3E%2C%20Vec%3C_%3E)%20%3D%20a%0A%20%20%20%20.into_iter()%0A%20%20%20%20.partition(%7Cn%7C%20n%20%25%202%20%3D%3D%200)%3B%0A%0Aassert_eq!(even%2C%20vec!%5B2%5D)%3B%0Aassert_eq!(odd%2C%20vec!%5B1%2C%203%5D)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2079-2082)

#### fn [partition\_in\_place](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.partition_in_place)<'a, T, P>(self, predicate: P) -> [usize](https://doc.rust-lang.org/std/primitive.usize.html)where  
    T: 'a,  
    Self: [DoubleEndedIterator](https://doc.rust-lang.org/std/iter/trait.DoubleEndedIterator.html "trait std::iter::DoubleEndedIterator")<Item = [&'a mut](https://doc.rust-lang.org/std/primitive.reference.html) T>,  
    P: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")([&](https://doc.rust-lang.org/std/primitive.reference.html)T) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

🔬 This is a nightly-only experimental API. (`iter_partition_in_place` [#62543](https://github.com/rust-lang/rust/issues/62543))

Reorders the elements of this iterator *in-place* according to the given predicate, such that all those that return `true` precede all those that return `false`. Returns the number of `true` elements found.

The relative order of partitioned items is not maintained.

##### [Current implementation](https://doc.rust-lang.org/std/iter/trait.Iterator.html#current-implementation)

Current algorithms tries finding the first element for which the predicate evaluates to false, and the last element for which it evaluates to true and repeatedly swaps them.

Time complexity: *O*(*n*)

See also [`is_partitioned()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.is_partitioned) and [`partition()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.partition).

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-33)

```
#![feature(iter_partition_in_place)]

let mut a = [1, 2, 3, 4, 5, 6, 7];

// Partition in-place between evens and odds
let i = a.iter_mut().partition_in_place(|&n| n % 2 == 0);

assert_eq!(i, 3);
assert!(a[..i].iter().all(|&n| n % 2 == 0)); // evens
assert!(a[i..].iter().all(|&n| n % 2 == 1)); // odds
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_partition_in_place)%5D%0A%0Afn%20main()%20%7B%0Alet%20mut%20a%20%3D%20%5B1%2C%202%2C%203%2C%204%2C%205%2C%206%2C%207%5D%3B%0A%0A%2F%2F%20Partition%20in-place%20between%20evens%20and%20odds%0Alet%20i%20%3D%20a.iter_mut().partition_in_place(%7C%26n%7C%20n%20%25%202%20%3D%3D%200)%3B%0A%0Aassert_eq!(i%2C%203)%3B%0Aassert!(a%5B..i%5D.iter().all(%7C%26n%7C%20n%20%25%202%20%3D%3D%200))%3B%20%2F%2F%20evens%0Aassert!(a%5Bi..%5D.iter().all(%7C%26n%7C%20n%20%25%202%20%3D%3D%201))%3B%20%2F%2F%20odds%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2136-2139)

#### fn [is\_partitioned](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.is_partitioned)<P>(self, predicate: P) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    P: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

🔬 This is a nightly-only experimental API. (`iter_is_partitioned` [#62544](https://github.com/rust-lang/rust/issues/62544))

Checks if the elements of this iterator are partitioned according to the given predicate, such that all those that return `true` precede all those that return `false`.

See also [`partition()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.partition) and [`partition_in_place()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.partition_in_place).

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-34)

```
#![feature(iter_is_partitioned)]

assert!("Iterator".chars().is_partitioned(char::is_uppercase));
assert!(!"IntoIterator".chars().is_partitioned(char::is_uppercase));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_is_partitioned)%5D%0A%0Afn%20main()%20%7B%0Aassert!(%22Iterator%22.chars().is_partitioned(char%3A%3Ais_uppercase))%3B%0Aassert!(!%22IntoIterator%22.chars().is_partitioned(char%3A%3Ais_uppercase))%3B%0A%7D&version=nightly&edition=2021)

1.27.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2230-2234)

#### fn [try\_fold](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.try_fold)<B, F, R>(&mut self, init: B, f: F) -> R where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(B, Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> R,  
    R: [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")<Output = B>, 

An iterator method that applies a function as long as it returns successfully, producing a single, final value.

`try_fold()` takes two arguments: an initial value, and a closure with two arguments: an ‘accumulator’, and an element. The closure either returns successfully, with the value that the accumulator should have for the next iteration, or it returns failure, with an error value that is propagated back to the caller immediately (short-circuiting).

The initial value is the value the accumulator will have on the first call. If applying the closure succeeded against every element of the iterator, `try_fold()` returns the final accumulator as success.

Folding is useful whenever you have a collection of something, and want to produce a single value from it.

##### [Note to Implementors](https://doc.rust-lang.org/std/iter/trait.Iterator.html#note-to-implementors)

Several of the other (forward) methods have default implementations in terms of this one, so try to implement this explicitly if it can do something better than the default `for` loop implementation.

In particular, try to have this call `try_fold()` on the internal parts from which this iterator is composed. If multiple calls are needed, the `?` operator may be convenient for chaining the accumulator value along, but beware any invariants that need to be upheld before those early returns. This is a `&mut self` method, so iteration needs to be resumable after hitting an error here.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-35)

Basic usage:

```
let a = [1, 2, 3];

// the checked sum of all of the elements of the array
let sum = a.iter().try_fold(0i8, |acc, &x| acc.checked_add(x));

assert_eq!(sum, Some(6));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0A%2F%2F%20the%20checked%20sum%20of%20all%20of%20the%20elements%20of%20the%20array%0Alet%20sum%20%3D%20a.iter().try_fold(0i8%2C%20%7Cacc%2C%20%26x%7C%20acc.checked_add(x))%3B%0A%0Aassert_eq!(sum%2C%20Some(6))%3B%0A%7D&edition=2021)

Short-circuiting:

```
let a = [10, 20, 30, 100, 40, 50];
let mut it = a.iter();

// This sum overflows when adding the 100 element
let sum = it.try_fold(0i8, |acc, &x| acc.checked_add(x));
assert_eq!(sum, None);

// Because it short-circuited, the remaining elements are still
// available through the iterator.
assert_eq!(it.len(), 2);
assert_eq!(it.next(), Some(&40));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B10%2C%2020%2C%2030%2C%20100%2C%2040%2C%2050%5D%3B%0Alet%20mut%20it%20%3D%20a.iter()%3B%0A%0A%2F%2F%20This%20sum%20overflows%20when%20adding%20the%20100%20element%0Alet%20sum%20%3D%20it.try_fold(0i8%2C%20%7Cacc%2C%20%26x%7C%20acc.checked_add(x))%3B%0Aassert_eq!(sum%2C%20None)%3B%0A%0A%2F%2F%20Because%20it%20short-circuited%2C%20the%20remaining%20elements%20are%20still%0A%2F%2F%20available%20through%20the%20iterator.%0Aassert_eq!(it.len()%2C%202)%3B%0Aassert_eq!(it.next()%2C%20Some(%2640))%3B%0A%7D&edition=2021)

While you cannot `break` from a closure, the [`ControlFlow`](https://doc.rust-lang.org/std/ops/enum.ControlFlow.html "ControlFlow") type allows a similar idea:

```
use std::ops::ControlFlow;

let triangular = (1..30).try_fold(0_i8, |prev, x| {
    if let Some(next) = prev.checked_add(x) {
        ControlFlow::Continue(next)
    } else {
        ControlFlow::Break(prev)
    }
});
assert_eq!(triangular, ControlFlow::Break(120));

let triangular = (1..30).try_fold(0_u64, |prev, x| {
    if let Some(next) = prev.checked_add(x) {
        ControlFlow::Continue(next)
    } else {
        ControlFlow::Break(prev)
    }
});
assert_eq!(triangular, ControlFlow::Continue(435));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Ause%20std%3A%3Aops%3A%3AControlFlow%3B%0A%0Alet%20triangular%20%3D%20(1..30).try_fold(0_i8%2C%20%7Cprev%2C%20x%7C%20%7B%0A%20%20%20%20if%20let%20Some(next)%20%3D%20prev.checked_add(x)%20%7B%0A%20%20%20%20%20%20%20%20ControlFlow%3A%3AContinue(next)%0A%20%20%20%20%7D%20else%20%7B%0A%20%20%20%20%20%20%20%20ControlFlow%3A%3ABreak(prev)%0A%20%20%20%20%7D%0A%7D)%3B%0Aassert_eq!(triangular%2C%20ControlFlow%3A%3ABreak(120))%3B%0A%0Alet%20triangular%20%3D%20(1..30).try_fold(0_u64%2C%20%7Cprev%2C%20x%7C%20%7B%0A%20%20%20%20if%20let%20Some(next)%20%3D%20prev.checked_add(x)%20%7B%0A%20%20%20%20%20%20%20%20ControlFlow%3A%3AContinue(next)%0A%20%20%20%20%7D%20else%20%7B%0A%20%20%20%20%20%20%20%20ControlFlow%3A%3ABreak(prev)%0A%20%20%20%20%7D%0A%7D)%3B%0Aassert_eq!(triangular%2C%20ControlFlow%3A%3AContinue(435))%3B%0A%7D&edition=2021)

1.27.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2288-2292)

#### fn [try\_for\_each](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.try_for_each)<F, R>(&mut self, f: F) -> R where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> R,  
    R: [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")<Output = [()](https://doc.rust-lang.org/std/primitive.unit.html)\>, 

An iterator method that applies a fallible function to each item in the iterator, stopping at the first error and returning that error.

This can also be thought of as the fallible form of [`for_each()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.for_each) or as the stateless version of [`try_fold()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.try_fold).

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-36)

```
use std::fs::rename;
use std::io::{stdout, Write};
use std::path::Path;

let data = ["no_tea.txt", "stale_bread.json", "torrential_rain.png"];

let res = data.iter().try_for_each(|x| writeln!(stdout(), "{x}"));
assert!(res.is_ok());

let mut it = data.iter().cloned();
let res = it.try_for_each(|x| rename(x, Path::new(x).with_extension("old")));
assert!(res.is_err());
// It short-circuited, so the remaining items are still in the iterator:
assert_eq!(it.next(), Some("stale_bread.json"));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Ause%20std%3A%3Afs%3A%3Arename%3B%0Ause%20std%3A%3Aio%3A%3A%7Bstdout%2C%20Write%7D%3B%0Ause%20std%3A%3Apath%3A%3APath%3B%0A%0Alet%20data%20%3D%20%5B%22no_tea.txt%22%2C%20%22stale_bread.json%22%2C%20%22torrential_rain.png%22%5D%3B%0A%0Alet%20res%20%3D%20data.iter().try_for_each(%7Cx%7C%20writeln!(stdout()%2C%20%22%7Bx%7D%22))%3B%0Aassert!(res.is_ok())%3B%0A%0Alet%20mut%20it%20%3D%20data.iter().cloned()%3B%0Alet%20res%20%3D%20it.try_for_each(%7Cx%7C%20rename(x%2C%20Path%3A%3Anew(x).with_extension(%22old%22)))%3B%0Aassert!(res.is_err())%3B%0A%2F%2F%20It%20short-circuited%2C%20so%20the%20remaining%20items%20are%20still%20in%20the%20iterator%3A%0Aassert_eq!(it.next()%2C%20Some(%22stale_bread.json%22))%3B%0A%7D&edition=2021)

The [`ControlFlow`](https://doc.rust-lang.org/std/ops/enum.ControlFlow.html "ControlFlow") type can be used with this method for the situations in which you’d use `break` and `continue` in a normal loop:

```
use std::ops::ControlFlow;

let r = (2..100).try_for_each(|x| {
    if 323 % x == 0 {
        return ControlFlow::Break(x)
    }

    ControlFlow::Continue(())
});
assert_eq!(r, ControlFlow::Break(17));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Ause%20std%3A%3Aops%3A%3AControlFlow%3B%0A%0Alet%20r%20%3D%20(2..100).try_for_each(%7Cx%7C%20%7B%0A%20%20%20%20if%20323%20%25%20x%20%3D%3D%200%20%7B%0A%20%20%20%20%20%20%20%20return%20ControlFlow%3A%3ABreak(x)%0A%20%20%20%20%7D%0A%0A%20%20%20%20ControlFlow%3A%3AContinue(())%0A%7D)%3B%0Aassert_eq!(r%2C%20ControlFlow%3A%3ABreak(17))%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2407-2410)

#### fn [fold](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.fold)<B, F>(self, init: B, f: F) -> B where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(B, Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> B, 

Folds every element into an accumulator by applying an operation, returning the final result.

`fold()` takes two arguments: an initial value, and a closure with two arguments: an ‘accumulator’, and an element. The closure returns the value that the accumulator should have for the next iteration.

The initial value is the value the accumulator will have on the first call.

After applying this closure to every element of the iterator, `fold()` returns the accumulator.

This operation is sometimes called ‘reduce’ or ‘inject’.

Folding is useful whenever you have a collection of something, and want to produce a single value from it.

Note: `fold()`, and similar methods that traverse the entire iterator, might not terminate for infinite iterators, even on traits for which a result is determinable in finite time.

Note: [`reduce()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.reduce) can be used to use the first element as the initial value, if the accumulator type and item type is the same.

Note: `fold()` combines elements in a *left-associative* fashion. For associative operators like `+`, the order the elements are combined in is not important, but for non-associative operators like `-` the order will affect the final result. For a *right-associative*version of `fold()`, see [`DoubleEndedIterator::rfold()`](https://doc.rust-lang.org/std/iter/trait.DoubleEndedIterator.html#method.rfold "DoubleEndedIterator::rfold()").

##### [Note to Implementors](https://doc.rust-lang.org/std/iter/trait.Iterator.html#note-to-implementors-1)

Several of the other (forward) methods have default implementations in terms of this one, so try to implement this explicitly if it can do something better than the default `for` loop implementation.

In particular, try to have this call `fold()` on the internal parts from which this iterator is composed.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-37)

Basic usage:

```
let a = [1, 2, 3];

// the sum of all of the elements of the array
let sum = a.iter().fold(0, |acc, x| acc + x);

assert_eq!(sum, 6);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0A%2F%2F%20the%20sum%20of%20all%20of%20the%20elements%20of%20the%20array%0Alet%20sum%20%3D%20a.iter().fold(0%2C%20%7Cacc%2C%20x%7C%20acc%20%2B%20x)%3B%0A%0Aassert_eq!(sum%2C%206)%3B%0A%7D&edition=2021)

Let’s walk through each step of the iteration here:

| element | acc | x | result |
| --- | --- | --- | --- |
|  | 0 |  |  |
| 1 | 0 | 1 | 1 |
| 2 | 1 | 2 | 3 |
| 3 | 3 | 3 | 6 |

And so, our final result, `6`.

This example demonstrates the left-associative nature of `fold()`: it builds a string, starting with an initial value and continuing with each element from the front until the back:

```
let numbers = [1, 2, 3, 4, 5];

let zero = "0".to_string();

let result = numbers.iter().fold(zero, |acc, &x| {
    format!("({acc} + {x})")
});

assert_eq!(result, "(((((0 + 1) + 2) + 3) + 4) + 5)");
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20numbers%20%3D%20%5B1%2C%202%2C%203%2C%204%2C%205%5D%3B%0A%0Alet%20zero%20%3D%20%220%22.to_string()%3B%0A%0Alet%20result%20%3D%20numbers.iter().fold(zero%2C%20%7Cacc%2C%20%26x%7C%20%7B%0A%20%20%20%20format!(%22(%7Bacc%7D%20%2B%20%7Bx%7D)%22)%0A%7D)%3B%0A%0Aassert_eq!(result%2C%20%22(((((0%20%2B%201)%20%2B%202)%20%2B%203)%20%2B%204)%20%2B%205)%22)%3B%0A%7D&edition=2021)

It’s common for people who haven’t used iterators a lot to use a `for` loop with a list of things to build up a result. Those can be turned into `fold()`s:

```
let numbers = [1, 2, 3, 4, 5];

let mut result = 0;

// for loop:
for i in &numbers {
    result = result + i;
}

// fold:
let result2 = numbers.iter().fold(0, |acc, &x| acc + x);

// they're the same
assert_eq!(result, result2);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20numbers%20%3D%20%5B1%2C%202%2C%203%2C%204%2C%205%5D%3B%0A%0Alet%20mut%20result%20%3D%200%3B%0A%0A%2F%2F%20for%20loop%3A%0Afor%20i%20in%20%26numbers%20%7B%0A%20%20%20%20result%20%3D%20result%20%2B%20i%3B%0A%7D%0A%0A%2F%2F%20fold%3A%0Alet%20result2%20%3D%20numbers.iter().fold(0%2C%20%7Cacc%2C%20%26x%7C%20acc%20%2B%20x)%3B%0A%0A%2F%2F%20they%27re%20the%20same%0Aassert_eq!(result%2C%20result2)%3B%0A%7D&edition=2021)

1.51.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2453-2456)

#### fn [reduce](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.reduce)<F>(self, f: F) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\> where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), 

Reduces the elements to a single one, by repeatedly applying a reducing operation.

If the iterator is empty, returns [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None"); otherwise, returns the result of the reduction.

The reducing function is a closure with two arguments: an ‘accumulator’, and an element. For iterators with at least one element, this is the same as [`fold()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.fold) with the first element of the iterator as the initial accumulator value, folding every subsequent element into it.

##### [Example](https://doc.rust-lang.org/std/iter/trait.Iterator.html#example)

Find the maximum value:

```
fn find_max<I>(iter: I) -> Option<I::Item>
    where I: Iterator,
          I::Item: Ord,
{
    iter.reduce(|accum, item| {
        if accum >= item { accum } else { item }
    })
}
let a = [10, 20, 5, -23, 0];
let b: [u32; 0] = [];

assert_eq!(find_max(a.iter()), Some(&20));
assert_eq!(find_max(b.iter()), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Afn%20find_max%3CI%3E(iter%3A%20I)%20-%3E%20Option%3CI%3A%3AItem%3E%0A%20%20%20%20where%20I%3A%20Iterator%2C%0A%20%20%20%20%20%20%20%20%20%20I%3A%3AItem%3A%20Ord%2C%0A%7B%0A%20%20%20%20iter.reduce(%7Caccum%2C%20item%7C%20%7B%0A%20%20%20%20%20%20%20%20if%20accum%20%3E%3D%20item%20%7B%20accum%20%7D%20else%20%7B%20item%20%7D%0A%20%20%20%20%7D)%0A%7D%0Alet%20a%20%3D%20%5B10%2C%2020%2C%205%2C%20-23%2C%200%5D%3B%0Alet%20b%3A%20%5Bu32%3B%200%5D%20%3D%20%5B%5D%3B%0A%0Aassert_eq!(find_max(a.iter())%2C%20Some(%2620))%3B%0Aassert_eq!(find_max(b.iter())%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2524-2529)

#### fn [try\_reduce](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.try_reduce)<F, R>(  
    &mut self,  
    f: F  
) -> <<R as [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")\>::[Residual](https://doc.rust-lang.org/std/ops/trait.Try.html#associatedtype.Residual "type std::ops::Try::Residual") as [Residual](https://doc.rust-lang.org/std/ops/trait.Residual.html "trait std::ops::Residual")<[Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<<R as [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")\>::[Output](https://doc.rust-lang.org/std/ops/trait.Try.html#associatedtype.Output "type std::ops::Try::Output")\>>>::[TryType](https://doc.rust-lang.org/std/ops/trait.Residual.html#associatedtype.TryType "type std::ops::Residual::TryType")where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> R,  
    R: [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")<Output = Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>,  
    <R as [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")\>::[Residual](https://doc.rust-lang.org/std/ops/trait.Try.html#associatedtype.Residual "type std::ops::Try::Residual"): [Residual](https://doc.rust-lang.org/std/ops/trait.Residual.html "trait std::ops::Residual")<[Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>>, 

🔬 This is a nightly-only experimental API. (`iterator_try_reduce` [#87053](https://github.com/rust-lang/rust/issues/87053))

Reduces the elements to a single one by repeatedly applying a reducing operation. If the closure returns a failure, the failure is propagated back to the caller immediately.

The return type of this method depends on the return type of the closure. If the closure returns `Result<Self::Item, E>`, then this function will return `Result<Option<Self::Item>, E>`. If the closure returns `Option<Self::Item>`, then this function will return `Option<Option<Self::Item>>`.

When called on an empty iterator, this function will return either `Some(None)` or `Ok(None)` depending on the type of the provided closure.

For iterators with at least one element, this is essentially the same as calling [`try_fold()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.try_fold) with the first element of the iterator as the initial accumulator value.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-38)

Safely calculate the sum of a series of numbers:

```
#![feature(iterator_try_reduce)]

let numbers: Vec<usize> = vec![10, 20, 5, 23, 0];
let sum = numbers.into_iter().try_reduce(|x, y| x.checked_add(y));
assert_eq!(sum, Some(Some(58)));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iterator_try_reduce)%5D%0A%0Afn%20main()%20%7B%0Alet%20numbers%3A%20Vec%3Cusize%3E%20%3D%20vec!%5B10%2C%2020%2C%205%2C%2023%2C%200%5D%3B%0Alet%20sum%20%3D%20numbers.into_iter().try_reduce(%7Cx%2C%20y%7C%20x.checked_add(y))%3B%0Aassert_eq!(sum%2C%20Some(Some(58)))%3B%0A%7D&version=nightly&edition=2021)

Determine when a reduction short circuited:

```
#![feature(iterator_try_reduce)]

let numbers = vec![1, 2, 3, usize::MAX, 4, 5];
let sum = numbers.into_iter().try_reduce(|x, y| x.checked_add(y));
assert_eq!(sum, None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iterator_try_reduce)%5D%0A%0Afn%20main()%20%7B%0Alet%20numbers%20%3D%20vec!%5B1%2C%202%2C%203%2C%20usize%3A%3AMAX%2C%204%2C%205%5D%3B%0Alet%20sum%20%3D%20numbers.into_iter().try_reduce(%7Cx%2C%20y%7C%20x.checked_add(y))%3B%0Aassert_eq!(sum%2C%20None)%3B%0A%7D&version=nightly&edition=2021)

Determine when a reduction was not performed because there are no elements:

```
#![feature(iterator_try_reduce)]

let numbers: Vec<usize> = Vec::new();
let sum = numbers.into_iter().try_reduce(|x, y| x.checked_add(y));
assert_eq!(sum, Some(None));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iterator_try_reduce)%5D%0A%0Afn%20main()%20%7B%0Alet%20numbers%3A%20Vec%3Cusize%3E%20%3D%20Vec%3A%3Anew()%3B%0Alet%20sum%20%3D%20numbers.into_iter().try_reduce(%7Cx%2C%20y%7C%20x.checked_add(y))%3B%0Aassert_eq!(sum%2C%20Some(None))%3B%0A%7D&version=nightly&edition=2021)

Use a [`Result`](https://doc.rust-lang.org/std/result/enum.Result.html "Result") instead of an [`Option`](https://doc.rust-lang.org/std/option/enum.Option.html "Option"):

```
#![feature(iterator_try_reduce)]

let numbers = vec!["1", "2", "3", "4", "5"];
let max: Result<Option<_>, <usize as std::str::FromStr>::Err> =
    numbers.into_iter().try_reduce(|x, y| {
        if x.parse::<usize>()? > y.parse::<usize>()? { Ok(x) } else { Ok(y) }
    });
assert_eq!(max, Ok(Some("5")));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iterator_try_reduce)%5D%0A%0Afn%20main()%20%7B%0Alet%20numbers%20%3D%20vec!%5B%221%22%2C%20%222%22%2C%20%223%22%2C%20%224%22%2C%20%225%22%5D%3B%0Alet%20max%3A%20Result%3COption%3C_%3E%2C%20%3Cusize%20as%20std%3A%3Astr%3A%3AFromStr%3E%3A%3AErr%3E%20%3D%0A%20%20%20%20numbers.into_iter().try_reduce(%7Cx%2C%20y%7C%20%7B%0A%20%20%20%20%20%20%20%20if%20x.parse%3A%3A%3Cusize%3E()%3F%20%3E%20y.parse%3A%3A%3Cusize%3E()%3F%20%7B%20Ok(x)%20%7D%20else%20%7B%20Ok(y)%20%7D%0A%20%20%20%20%7D)%3B%0Aassert_eq!(max%2C%20Ok(Some(%225%22)))%3B%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2581-2584)

#### fn [all](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.all)<F>(&mut self, f: F) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

Tests if every element of the iterator matches a predicate.

`all()` takes a closure that returns `true` or `false`. It applies this closure to each element of the iterator, and if they all return `true`, then so does `all()`. If any of them return `false`, it returns `false`.

`all()` is short-circuiting; in other words, it will stop processing as soon as it finds a `false`, given that no matter what else happens, the result will also be `false`.

An empty iterator returns `true`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-39)

Basic usage:

```
let a = [1, 2, 3];

assert!(a.iter().all(|&x| x > 0));

assert!(!a.iter().all(|&x| x > 2));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Aassert!(a.iter().all(%7C%26x%7C%20x%20%3E%200))%3B%0A%0Aassert!(!a.iter().all(%7C%26x%7C%20x%20%3E%202))%3B%0A%7D&edition=2021)

Stopping at the first `false`:

```
let a = [1, 2, 3];

let mut iter = a.iter();

assert!(!iter.all(|&x| x != 2));

// we can still use `iter`, as there are more elements.
assert_eq!(iter.next(), Some(&3));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0Aassert!(!iter.all(%7C%26x%7C%20x%20!%3D%202))%3B%0A%0A%2F%2F%20we%20can%20still%20use%20%60iter%60%2C%20as%20there%20are%20more%20elements.%0Aassert_eq!(iter.next()%2C%20Some(%263))%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2634-2637)

#### fn [any](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.any)<F>(&mut self, f: F) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

Tests if any element of the iterator matches a predicate.

`any()` takes a closure that returns `true` or `false`. It applies this closure to each element of the iterator, and if any of them return`true`, then so does `any()`. If they all return `false`, it returns `false`.

`any()` is short-circuiting; in other words, it will stop processing as soon as it finds a `true`, given that no matter what else happens, the result will also be `true`.

An empty iterator returns `false`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-40)

Basic usage:

```
let a = [1, 2, 3];

assert!(a.iter().any(|&x| x > 0));

assert!(!a.iter().any(|&x| x > 5));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Aassert!(a.iter().any(%7C%26x%7C%20x%20%3E%200))%3B%0A%0Aassert!(!a.iter().any(%7C%26x%7C%20x%20%3E%205))%3B%0A%7D&edition=2021)

Stopping at the first `true`:

```
let a = [1, 2, 3];

let mut iter = a.iter();

assert!(iter.any(|&x| x != 2));

// we can still use `iter`, as there are more elements.
assert_eq!(iter.next(), Some(&2));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0Aassert!(iter.any(%7C%26x%7C%20x%20!%3D%202))%3B%0A%0A%2F%2F%20we%20can%20still%20use%20%60iter%60%2C%20as%20there%20are%20more%20elements.%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2694-2697)

#### fn [find](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.find)<P>(&mut self, predicate: P) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\> where  
    P: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

Searches for an element of an iterator that satisfies a predicate.

`find()` takes a closure that returns `true` or `false`. It applies this closure to each element of the iterator, and if any of them return `true`, then `find()` returns [`Some(element)`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.Some). If they all return `false`, it returns [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None").

`find()` is short-circuiting; in other words, it will stop processing as soon as the closure returns `true`.

Because `find()` takes a reference, and many iterators iterate over references, this leads to a possibly confusing situation where the argument is a double reference. You can see this effect in the examples below, with `&&x`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-41)

Basic usage:

```
let a = [1, 2, 3];

assert_eq!(a.iter().find(|&&x| x == 2), Some(&2));

assert_eq!(a.iter().find(|&&x| x == 5), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Aassert_eq!(a.iter().find(%7C%26%26x%7C%20x%20%3D%3D%202)%2C%20Some(%262))%3B%0A%0Aassert_eq!(a.iter().find(%7C%26%26x%7C%20x%20%3D%3D%205)%2C%20None)%3B%0A%7D&edition=2021)

Stopping at the first `true`:

```
let a = [1, 2, 3];

let mut iter = a.iter();

assert_eq!(iter.find(|&&x| x == 2), Some(&2));

// we can still use `iter`, as there are more elements.
assert_eq!(iter.next(), Some(&3));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0Aassert_eq!(iter.find(%7C%26%26x%7C%20x%20%3D%3D%202)%2C%20Some(%262))%3B%0A%0A%2F%2F%20we%20can%20still%20use%20%60iter%60%2C%20as%20there%20are%20more%20elements.%0Aassert_eq!(iter.next()%2C%20Some(%263))%3B%0A%7D&edition=2021)

Note that `iter.find(f)` is equivalent to `iter.filter(f).next()`.

1.30.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2725-2728)

#### fn [find\_map](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.find_map)<B, F>(&mut self, f: F) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<B> where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<B>, 

Applies function to the elements of iterator and returns the first non-none result.

`iter.find_map(f)` is equivalent to `iter.filter_map(f).next()`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-42)

```
let a = ["lol", "NaN", "2", "5"];

let first_number = a.iter().find_map(|s| s.parse().ok());

assert_eq!(first_number, Some(2));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B%22lol%22%2C%20%22NaN%22%2C%20%222%22%2C%20%225%22%5D%3B%0A%0Alet%20first_number%20%3D%20a.iter().find_map(%7Cs%7C%20s.parse().ok())%3B%0A%0Aassert_eq!(first_number%2C%20Some(2))%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2781-2786)

#### fn [try\_find](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.try_find)<F, R>(  
    &mut self,  
    f: F  
) -> <<R as [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")\>::[Residual](https://doc.rust-lang.org/std/ops/trait.Try.html#associatedtype.Residual "type std::ops::Try::Residual") as [Residual](https://doc.rust-lang.org/std/ops/trait.Residual.html "trait std::ops::Residual")<[Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>>>::[TryType](https://doc.rust-lang.org/std/ops/trait.Residual.html#associatedtype.TryType "type std::ops::Residual::TryType")where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> R,  
    R: [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")<Output = [bool](https://doc.rust-lang.org/std/primitive.bool.html)\>,  
    <R as [Try](https://doc.rust-lang.org/std/ops/trait.Try.html "trait std::ops::Try")\>::[Residual](https://doc.rust-lang.org/std/ops/trait.Try.html#associatedtype.Residual "type std::ops::Try::Residual"): [Residual](https://doc.rust-lang.org/std/ops/trait.Residual.html "trait std::ops::Residual")<[Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>>, 

🔬 This is a nightly-only experimental API. (`try_find` [#63178](https://github.com/rust-lang/rust/issues/63178))

Applies function to the elements of iterator and returns the first true result or the first error.

The return type of this method depends on the return type of the closure. If you return `Result<bool, E>` from the closure, you’ll get a `Result<Option<Self::Item>; E>`. If you return `Option<bool>` from the closure, you’ll get an `Option<Option<Self::Item>>`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-43)

```
#![feature(try_find)]

let a = ["1", "2", "lol", "NaN", "5"];

let is_my_num = |s: &str, search: i32| -> Result<bool, std::num::ParseIntError> {
    Ok(s.parse::<i32>()?  == search)
};

let result = a.iter().try_find(|&&s| is_my_num(s, 2));
assert_eq!(result, Ok(Some(&"2")));

let result = a.iter().try_find(|&&s| is_my_num(s, 5));
assert!(result.is_err());
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(try_find)%5D%0A%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B%221%22%2C%20%222%22%2C%20%22lol%22%2C%20%22NaN%22%2C%20%225%22%5D%3B%0A%0Alet%20is_my_num%20%3D%20%7Cs%3A%20%26str%2C%20search%3A%20i32%7C%20-%3E%20Result%3Cbool%2C%20std%3A%3Anum%3A%3AParseIntError%3E%20%7B%0A%20%20%20%20Ok(s.parse%3A%3A%3Ci32%3E()%3F%20%20%3D%3D%20search)%0A%7D%3B%0A%0Alet%20result%20%3D%20a.iter().try_find(%7C%26%26s%7C%20is_my_num(s%2C%202))%3B%0Aassert_eq!(result%2C%20Ok(Some(%26%222%22)))%3B%0A%0Alet%20result%20%3D%20a.iter().try_find(%7C%26%26s%7C%20is_my_num(s%2C%205))%3B%0Aassert!(result.is_err())%3B%0A%7D&version=nightly&edition=2021)

This also supports other types which implement `Try`, not just `Result`.

```
#![feature(try_find)]

use std::num::NonZeroU32;
let a = [3, 5, 7, 4, 9, 0, 11];
let result = a.iter().try_find(|&&x| NonZeroU32::new(x).map(|y| y.is_power_of_two()));
assert_eq!(result, Some(Some(&4)));
let result = a.iter().take(3).try_find(|&&x| NonZeroU32::new(x).map(|y| y.is_power_of_two()));
assert_eq!(result, Some(None));
let result = a.iter().rev().try_find(|&&x| NonZeroU32::new(x).map(|y| y.is_power_of_two()));
assert_eq!(result, None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(try_find)%5D%0A%0Afn%20main()%20%7B%0Ause%20std%3A%3Anum%3A%3ANonZeroU32%3B%0Alet%20a%20%3D%20%5B3%2C%205%2C%207%2C%204%2C%209%2C%200%2C%2011%5D%3B%0Alet%20result%20%3D%20a.iter().try_find(%7C%26%26x%7C%20NonZeroU32%3A%3Anew(x).map(%7Cy%7C%20y.is_power_of_two()))%3B%0Aassert_eq!(result%2C%20Some(Some(%264)))%3B%0Alet%20result%20%3D%20a.iter().take(3).try_find(%7C%26%26x%7C%20NonZeroU32%3A%3Anew(x).map(%7Cy%7C%20y.is_power_of_two()))%3B%0Aassert_eq!(result%2C%20Some(None))%3B%0Alet%20result%20%3D%20a.iter().rev().try_find(%7C%26%26x%7C%20NonZeroU32%3A%3Anew(x).map(%7Cy%7C%20y.is_power_of_two()))%3B%0Aassert_eq!(result%2C%20None)%3B%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2863-2866)

#### fn [position](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.position)<P>(&mut self, predicate: P) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<[usize](https://doc.rust-lang.org/std/primitive.usize.html)\> where  
    P: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

Searches for an element in an iterator, returning its index.

`position()` takes a closure that returns `true` or `false`. It applies this closure to each element of the iterator, and if one of them returns `true`, then `position()` returns [`Some(index)`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.Some). If all of them return `false`, it returns [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None").

`position()` is short-circuiting; in other words, it will stop processing as soon as it finds a `true`.

##### [Overflow Behavior](https://doc.rust-lang.org/std/iter/trait.Iterator.html#overflow-behavior-2)

The method does no guarding against overflows, so if there are more than [`usize::MAX`](https://doc.rust-lang.org/std/primitive.usize.html#associatedconstant.MAX "usize::MAX") non-matching elements, it either produces the wrong result or panics. If debug assertions are enabled, a panic is guaranteed.

##### [Panics](https://doc.rust-lang.org/std/iter/trait.Iterator.html#panics-3)

This function might panic if the iterator has more than `usize::MAX` non-matching elements.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-44)

Basic usage:

```
let a = [1, 2, 3];

assert_eq!(a.iter().position(|&x| x == 2), Some(1));

assert_eq!(a.iter().position(|&x| x == 5), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Aassert_eq!(a.iter().position(%7C%26x%7C%20x%20%3D%3D%202)%2C%20Some(1))%3B%0A%0Aassert_eq!(a.iter().position(%7C%26x%7C%20x%20%3D%3D%205)%2C%20None)%3B%0A%7D&edition=2021)

Stopping at the first `true`:

```
let a = [1, 2, 3, 4];

let mut iter = a.iter();

assert_eq!(iter.position(|&x| x >= 2), Some(1));

// we can still use `iter`, as there are more elements.
assert_eq!(iter.next(), Some(&3));

// The returned index depends on iterator state
assert_eq!(iter.position(|&x| x == 4), Some(0));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%2C%204%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0Aassert_eq!(iter.position(%7C%26x%7C%20x%20%3E%3D%202)%2C%20Some(1))%3B%0A%0A%2F%2F%20we%20can%20still%20use%20%60iter%60%2C%20as%20there%20are%20more%20elements.%0Aassert_eq!(iter.next()%2C%20Some(%263))%3B%0A%0A%2F%2F%20The%20returned%20index%20depends%20on%20iterator%20state%0Aassert_eq!(iter.position(%7C%26x%7C%20x%20%3D%3D%204)%2C%20Some(0))%3B%0A%7D&edition=2021)
[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2920-2923)

#### fn [rposition](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.rposition)<P>(&mut self, predicate: P) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<[usize](https://doc.rust-lang.org/std/primitive.usize.html)\> where  
    P: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html),  
    Self: [ExactSizeIterator](https://doc.rust-lang.org/std/iter/trait.ExactSizeIterator.html "trait std::iter::ExactSizeIterator") + [DoubleEndedIterator](https://doc.rust-lang.org/std/iter/trait.DoubleEndedIterator.html "trait std::iter::DoubleEndedIterator"), 

Searches for an element in an iterator from the right, returning its index.

`rposition()` takes a closure that returns `true` or `false`. It applies this closure to each element of the iterator, starting from the end, and if one of them returns `true`, then `rposition()` returns [`Some(index)`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.Some). If all of them return `false`, it returns [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None").

`rposition()` is short-circuiting; in other words, it will stop processing as soon as it finds a `true`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-45)

Basic usage:

```
let a = [1, 2, 3];

assert_eq!(a.iter().rposition(|&x| x == 3), Some(2));

assert_eq!(a.iter().rposition(|&x| x == 5), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Aassert_eq!(a.iter().rposition(%7C%26x%7C%20x%20%3D%3D%203)%2C%20Some(2))%3B%0A%0Aassert_eq!(a.iter().rposition(%7C%26x%7C%20x%20%3D%3D%205)%2C%20None)%3B%0A%7D&edition=2021)

Stopping at the first `true`:

```
let a = [1, 2, 3];

let mut iter = a.iter();

assert_eq!(iter.rposition(|&x| x == 2), Some(1));

// we can still use `iter`, as there are more elements.
assert_eq!(iter.next(), Some(&1));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter()%3B%0A%0Aassert_eq!(iter.rposition(%7C%26x%7C%20x%20%3D%3D%202)%2C%20Some(1))%3B%0A%0A%2F%2F%20we%20can%20still%20use%20%60iter%60%2C%20as%20there%20are%20more%20elements.%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#2971-2974)

#### fn [max](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.max)(self) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\> where  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [Ord](https://doc.rust-lang.org/std/cmp/trait.Ord.html "trait std::cmp::Ord"), 

Returns the maximum element of an iterator.

If several elements are equally maximum, the last element is returned. If the iterator is empty, [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is returned.

Note that [`f32`](https://doc.rust-lang.org/std/primitive.f32.html "f32")/[`f64`](https://doc.rust-lang.org/std/primitive.f64.html "f64") doesn’t implement [`Ord`](https://doc.rust-lang.org/std/cmp/trait.Ord.html "Ord") due to NaN being incomparable. You can work around this by using [`Iterator::reduce`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.reduce "Iterator::reduce"):

```
assert_eq!(
    [2.4, f32::NAN, 1.3]
        .into_iter()
        .reduce(f32::max)
        .unwrap(),
    2.4
);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Aassert_eq!(%0A%20%20%20%20%5B2.4%2C%20f32%3A%3ANAN%2C%201.3%5D%0A%20%20%20%20%20%20%20%20.into_iter()%0A%20%20%20%20%20%20%20%20.reduce(f32%3A%3Amax)%0A%20%20%20%20%20%20%20%20.unwrap()%2C%0A%20%20%20%202.4%0A)%3B%0A%7D&edition=2021)

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-46)

Basic usage:

```
let a = [1, 2, 3];
let b: Vec<u32> = Vec::new();

assert_eq!(a.iter().max(), Some(&3));
assert_eq!(b.iter().max(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20b%3A%20Vec%3Cu32%3E%20%3D%20Vec%3A%3Anew()%3B%0A%0Aassert_eq!(a.iter().max()%2C%20Some(%263))%3B%0Aassert_eq!(b.iter().max()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3009-3012)

#### fn [min](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.min)(self) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\> where  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [Ord](https://doc.rust-lang.org/std/cmp/trait.Ord.html "trait std::cmp::Ord"), 

Returns the minimum element of an iterator.

If several elements are equally minimum, the first element is returned. If the iterator is empty, [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is returned.

Note that [`f32`](https://doc.rust-lang.org/std/primitive.f32.html "f32")/[`f64`](https://doc.rust-lang.org/std/primitive.f64.html "f64") doesn’t implement [`Ord`](https://doc.rust-lang.org/std/cmp/trait.Ord.html "Ord") due to NaN being incomparable. You can work around this by using [`Iterator::reduce`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.reduce "Iterator::reduce"):

```
assert_eq!(
    [2.4, f32::NAN, 1.3]
        .into_iter()
        .reduce(f32::min)
        .unwrap(),
    1.3
);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Aassert_eq!(%0A%20%20%20%20%5B2.4%2C%20f32%3A%3ANAN%2C%201.3%5D%0A%20%20%20%20%20%20%20%20.into_iter()%0A%20%20%20%20%20%20%20%20.reduce(f32%3A%3Amin)%0A%20%20%20%20%20%20%20%20.unwrap()%2C%0A%20%20%20%201.3%0A)%3B%0A%7D&edition=2021)

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-47)

Basic usage:

```
let a = [1, 2, 3];
let b: Vec<u32> = Vec::new();

assert_eq!(a.iter().min(), Some(&1));
assert_eq!(b.iter().min(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20b%3A%20Vec%3Cu32%3E%20%3D%20Vec%3A%3Anew()%3B%0A%0Aassert_eq!(a.iter().min()%2C%20Some(%261))%3B%0Aassert_eq!(b.iter().min()%2C%20None)%3B%0A%7D&edition=2021)

1.6.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3031-3034)

#### fn [max\_by\_key](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.max_by_key)<B, F>(self, f: F) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\> where  
    B: [Ord](https://doc.rust-lang.org/std/cmp/trait.Ord.html "trait std::cmp::Ord"),  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> B, 

Returns the element that gives the maximum value from the specified function.

If several elements are equally maximum, the last element is returned. If the iterator is empty, [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is returned.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-48)

```
let a = [-3_i32, 0, 1, 5, -10];
assert_eq!(*a.iter().max_by_key(|x| x.abs()).unwrap(), -10);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-3_i32%2C%200%2C%201%2C%205%2C%20-10%5D%3B%0Aassert_eq!(*a.iter().max_by_key(%7Cx%7C%20x.abs()).unwrap()%2C%20-10)%3B%0A%7D&edition=2021)

1.15.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3064-3067)

#### fn [max\_by](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.max_by)<F>(self, compare: F) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\> where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), &Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [Ordering](https://doc.rust-lang.org/std/cmp/enum.Ordering.html "enum std::cmp::Ordering"), 

Returns the element that gives the maximum value with respect to the specified comparison function.

If several elements are equally maximum, the last element is returned. If the iterator is empty, [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is returned.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-49)

```
let a = [-3_i32, 0, 1, 5, -10];
assert_eq!(*a.iter().max_by(|x, y| x.cmp(y)).unwrap(), 5);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-3_i32%2C%200%2C%201%2C%205%2C%20-10%5D%3B%0Aassert_eq!(*a.iter().max_by(%7Cx%2C%20y%7C%20x.cmp(y)).unwrap()%2C%205)%3B%0A%7D&edition=2021)

1.6.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3091-3094)

#### fn [min\_by\_key](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.min_by_key)<B, F>(self, f: F) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\> where  
    B: [Ord](https://doc.rust-lang.org/std/cmp/trait.Ord.html "trait std::cmp::Ord"),  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> B, 

Returns the element that gives the minimum value from the specified function.

If several elements are equally minimum, the first element is returned. If the iterator is empty, [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is returned.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-50)

```
let a = [-3_i32, 0, 1, 5, -10];
assert_eq!(*a.iter().min_by_key(|x| x.abs()).unwrap(), 0);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-3_i32%2C%200%2C%201%2C%205%2C%20-10%5D%3B%0Aassert_eq!(*a.iter().min_by_key(%7Cx%7C%20x.abs()).unwrap()%2C%200)%3B%0A%7D&edition=2021)

1.15.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3124-3127)

#### fn [min\_by](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.min_by)<F>(self, compare: F) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\> where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), &Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [Ordering](https://doc.rust-lang.org/std/cmp/enum.Ordering.html "enum std::cmp::Ordering"), 

Returns the element that gives the minimum value with respect to the specified comparison function.

If several elements are equally minimum, the first element is returned. If the iterator is empty, [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None") is returned.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-51)

```
let a = [-3_i32, 0, 1, 5, -10];
assert_eq!(*a.iter().min_by(|x, y| x.cmp(y)).unwrap(), -10);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B-3_i32%2C%200%2C%201%2C%205%2C%20-10%5D%3B%0Aassert_eq!(*a.iter().min_by(%7Cx%2C%20y%7C%20x.cmp(y)).unwrap()%2C%20-10)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3161-3163)

#### fn [rev](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.rev)(self) -> [Rev](https://doc.rust-lang.org/std/iter/struct.Rev.html "struct std::iter::Rev")<Self>ⓘwhere  
    Self: [DoubleEndedIterator](https://doc.rust-lang.org/std/iter/trait.DoubleEndedIterator.html "trait std::iter::DoubleEndedIterator"), 

Reverses an iterator’s direction.

Usually, iterators iterate from left to right. After using `rev()`, an iterator will instead iterate from right to left.

This is only possible if the iterator has an end, so `rev()` only works on [`DoubleEndedIterator`](https://doc.rust-lang.org/std/iter/trait.DoubleEndedIterator.html "DoubleEndedIterator")s.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-52)

```
let a = [1, 2, 3];

let mut iter = a.iter().rev();

assert_eq!(iter.next(), Some(&3));
assert_eq!(iter.next(), Some(&2));
assert_eq!(iter.next(), Some(&1));

assert_eq!(iter.next(), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20iter%20%3D%20a.iter().rev()%3B%0A%0Aassert_eq!(iter.next()%2C%20Some(%263))%3B%0Aassert_eq!(iter.next()%2C%20Some(%262))%3B%0Aassert_eq!(iter.next()%2C%20Some(%261))%3B%0A%0Aassert_eq!(iter.next()%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3199-3203)

#### fn [unzip](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.unzip)<A, B, FromA, FromB>(self) -> [(FromA, FromB)](https://doc.rust-lang.org/std/primitive.tuple.html)where  
    FromA: [Default](https://doc.rust-lang.org/std/default/trait.Default.html "trait std::default::Default") + [Extend](https://doc.rust-lang.org/std/iter/trait.Extend.html "trait std::iter::Extend")<A>,  
    FromB: [Default](https://doc.rust-lang.org/std/default/trait.Default.html "trait std::default::Default") + [Extend](https://doc.rust-lang.org/std/iter/trait.Extend.html "trait std::iter::Extend")<B>,  
    Self: [Iterator](https://doc.rust-lang.org/std/iter/trait.Iterator.html "trait std::iter::Iterator")<Item = [(A, B)](https://doc.rust-lang.org/std/primitive.tuple.html)\>, 

Converts an iterator of pairs into a pair of containers.

`unzip()` consumes an entire iterator of pairs, producing two collections: one from the left elements of the pairs, and one from the right elements.

This function is, in some sense, the opposite of [`zip`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.zip).

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-53)

Basic usage:

```
let a = [(1, 2), (3, 4), (5, 6)];

let (left, right): (Vec<_>, Vec<_>) = a.iter().cloned().unzip();

assert_eq!(left, [1, 3, 5]);
assert_eq!(right, [2, 4, 6]);

// you can also unzip multiple nested tuples at once
let a = [(1, (2, 3)), (4, (5, 6))];

let (x, (y, z)): (Vec<_>, (Vec<_>, Vec<_>)) = a.iter().cloned().unzip();
assert_eq!(x, [1, 4]);
assert_eq!(y, [2, 5]);
assert_eq!(z, [3, 6]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B(1%2C%202)%2C%20(3%2C%204)%2C%20(5%2C%206)%5D%3B%0A%0Alet%20(left%2C%20right)%3A%20(Vec%3C_%3E%2C%20Vec%3C_%3E)%20%3D%20a.iter().cloned().unzip()%3B%0A%0Aassert_eq!(left%2C%20%5B1%2C%203%2C%205%5D)%3B%0Aassert_eq!(right%2C%20%5B2%2C%204%2C%206%5D)%3B%0A%0A%2F%2F%20you%20can%20also%20unzip%20multiple%20nested%20tuples%20at%20once%0Alet%20a%20%3D%20%5B(1%2C%20(2%2C%203))%2C%20(4%2C%20(5%2C%206))%5D%3B%0A%0Alet%20(x%2C%20(y%2C%20z))%3A%20(Vec%3C_%3E%2C%20(Vec%3C_%3E%2C%20Vec%3C_%3E))%20%3D%20a.iter().cloned().unzip()%3B%0Aassert_eq!(x%2C%20%5B1%2C%204%5D)%3B%0Aassert_eq!(y%2C%20%5B2%2C%205%5D)%3B%0Aassert_eq!(z%2C%20%5B3%2C%206%5D)%3B%0A%7D&edition=2021)

1.36.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3231-3234)

#### fn [copied](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.copied)<'a, T>(self) -> [Copied](https://doc.rust-lang.org/std/iter/struct.Copied.html "struct std::iter::Copied")<Self>ⓘwhere  
    T: 'a + [Copy](https://doc.rust-lang.org/std/marker/trait.Copy.html "trait std::marker::Copy"),  
    Self: [Iterator](https://doc.rust-lang.org/std/iter/trait.Iterator.html "trait std::iter::Iterator")<Item = [&'a](https://doc.rust-lang.org/std/primitive.reference.html) T>, 

Creates an iterator which copies all of its elements.

This is useful when you have an iterator over `&T`, but you need an iterator over `T`.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-54)

Basic usage:

```
let a = [1, 2, 3];

let v_copied: Vec<_> = a.iter().copied().collect();

// copied is the same as .map(|&x| x)
let v_map: Vec<_> = a.iter().map(|&x| x).collect();

assert_eq!(v_copied, vec![1, 2, 3]);
assert_eq!(v_map, vec![1, 2, 3]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20v_copied%3A%20Vec%3C_%3E%20%3D%20a.iter().copied().collect()%3B%0A%0A%2F%2F%20copied%20is%20the%20same%20as%20.map(%7C%26x%7C%20x)%0Alet%20v_map%3A%20Vec%3C_%3E%20%3D%20a.iter().map(%7C%26x%7C%20x).collect()%3B%0A%0Aassert_eq!(v_copied%2C%20vec!%5B1%2C%202%2C%203%5D)%3B%0Aassert_eq!(v_map%2C%20vec!%5B1%2C%202%2C%203%5D)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3278-3281)

#### fn [cloned](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.cloned)<'a, T>(self) -> [Cloned](https://doc.rust-lang.org/std/iter/struct.Cloned.html "struct std::iter::Cloned")<Self>ⓘwhere  
    T: 'a + [Clone](https://doc.rust-lang.org/std/clone/trait.Clone.html "trait std::clone::Clone"),  
    Self: [Iterator](https://doc.rust-lang.org/std/iter/trait.Iterator.html "trait std::iter::Iterator")<Item = [&'a](https://doc.rust-lang.org/std/primitive.reference.html) T>, 

Creates an iterator which [`clone`](https://doc.rust-lang.org/std/clone/trait.Clone.html#tymethod.clone)s all of its elements.

This is useful when you have an iterator over `&T`, but you need an iterator over `T`.

There is no guarantee whatsoever about the `clone` method actually being called *or* optimized away. So code should not depend on either.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-55)

Basic usage:

```
let a = [1, 2, 3];

let v_cloned: Vec<_> = a.iter().cloned().collect();

// cloned is the same as .map(|&x| x), for integers
let v_map: Vec<_> = a.iter().map(|&x| x).collect();

assert_eq!(v_cloned, vec![1, 2, 3]);
assert_eq!(v_map, vec![1, 2, 3]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20v_cloned%3A%20Vec%3C_%3E%20%3D%20a.iter().cloned().collect()%3B%0A%0A%2F%2F%20cloned%20is%20the%20same%20as%20.map(%7C%26x%7C%20x)%2C%20for%20integers%0Alet%20v_map%3A%20Vec%3C_%3E%20%3D%20a.iter().map(%7C%26x%7C%20x).collect()%3B%0A%0Aassert_eq!(v_cloned%2C%20vec!%5B1%2C%202%2C%203%5D)%3B%0Aassert_eq!(v_map%2C%20vec!%5B1%2C%202%2C%203%5D)%3B%0A%7D&edition=2021)

To get the best performance, try to clone late:

```
let a = [vec![0_u8, 1, 2], vec![3, 4], vec![23]];
// don't do this:
let slower: Vec<_> = a.iter().cloned().filter(|s| s.len() == 1).collect();
assert_eq!(&[vec![23]], &slower[..]);
// instead call `cloned` late
let faster: Vec<_> = a.iter().filter(|s| s.len() == 1).cloned().collect();
assert_eq!(&[vec![23]], &faster[..]);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5Bvec!%5B0_u8%2C%201%2C%202%5D%2C%20vec!%5B3%2C%204%5D%2C%20vec!%5B23%5D%5D%3B%0A%2F%2F%20don%27t%20do%20this%3A%0Alet%20slower%3A%20Vec%3C_%3E%20%3D%20a.iter().cloned().filter(%7Cs%7C%20s.len()%20%3D%3D%201).collect()%3B%0Aassert_eq!(%26%5Bvec!%5B23%5D%5D%2C%20%26slower%5B..%5D)%3B%0A%2F%2F%20instead%20call%20%60cloned%60%20late%0Alet%20faster%3A%20Vec%3C_%3E%20%3D%20a.iter().filter(%7Cs%7C%20s.len()%20%3D%3D%201).cloned().collect()%3B%0Aassert_eq!(%26%5Bvec!%5B23%5D%5D%2C%20%26faster%5B..%5D)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3312-3314)

#### fn [cycle](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.cycle)(self) -> [Cycle](https://doc.rust-lang.org/std/iter/struct.Cycle.html "struct std::iter::Cycle")<Self>ⓘwhere  
    Self: [Clone](https://doc.rust-lang.org/std/clone/trait.Clone.html "trait std::clone::Clone"), 

Repeats an iterator endlessly.

Instead of stopping at [`None`](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None "None"), the iterator will instead start again, from the beginning. After iterating again, it will start at the beginning again. And again. And again. Forever. Note that in case the original iterator is empty, the resulting iterator will also be empty.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-56)

Basic usage:

```
let a = [1, 2, 3];

let mut it = a.iter().cycle();

assert_eq!(it.next(), Some(&1));
assert_eq!(it.next(), Some(&2));
assert_eq!(it.next(), Some(&3));
assert_eq!(it.next(), Some(&1));
assert_eq!(it.next(), Some(&2));
assert_eq!(it.next(), Some(&3));
assert_eq!(it.next(), Some(&1));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0A%0Alet%20mut%20it%20%3D%20a.iter().cycle()%3B%0A%0Aassert_eq!(it.next()%2C%20Some(%261))%3B%0Aassert_eq!(it.next()%2C%20Some(%262))%3B%0Aassert_eq!(it.next()%2C%20Some(%263))%3B%0Aassert_eq!(it.next()%2C%20Some(%261))%3B%0Aassert_eq!(it.next()%2C%20Some(%262))%3B%0Aassert_eq!(it.next()%2C%20Some(%263))%3B%0Aassert_eq!(it.next()%2C%20Some(%261))%3B%0A%7D&edition=2021)

1.11.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3342-3345)

#### fn [sum](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.sum)<S>(self) -> S where  
    S: [Sum](https://doc.rust-lang.org/std/iter/trait.Sum.html "trait std::iter::Sum")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>, 

Sums the elements of an iterator.

Takes each element, adds them together, and returns the result.

An empty iterator returns the zero value of the type.

##### [Panics](https://doc.rust-lang.org/std/iter/trait.Iterator.html#panics-4)

When calling `sum()` and a primitive integer type is being returned, this method will panic if the computation overflows and debug assertions are enabled.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-57)

Basic usage:

```
let a = [1, 2, 3];
let sum: i32 = a.iter().sum();

assert_eq!(sum, 6);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Alet%20a%20%3D%20%5B1%2C%202%2C%203%5D%3B%0Alet%20sum%3A%20i32%20%3D%20a.iter().sum()%3B%0A%0Aassert_eq!(sum%2C%206)%3B%0A%7D&edition=2021)

1.11.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3371-3374)

#### fn [product](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.product)<P>(self) -> P where  
    P: [Product](https://doc.rust-lang.org/std/iter/trait.Product.html "trait std::iter::Product")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>, 

Iterates over the entire iterator, multiplying all the elements

An empty iterator returns the one value of the type.

##### [Panics](https://doc.rust-lang.org/std/iter/trait.Iterator.html#panics-5)

When calling `product()` and a primitive integer type is being returned, method will panic if the computation overflows and debug assertions are enabled.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-58)

```
fn factorial(n: u32) -> u32 {
    (1..=n).product()
}
assert_eq!(factorial(0), 1);
assert_eq!(factorial(1), 1);
assert_eq!(factorial(5), 120);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Afn%20factorial(n%3A%20u32)%20-%3E%20u32%20%7B%0A%20%20%20%20(1..%3Dn).product()%0A%7D%0Aassert_eq!(factorial(0)%2C%201)%3B%0Aassert_eq!(factorial(1)%2C%201)%3B%0Aassert_eq!(factorial(5)%2C%20120)%3B%0A%7D&edition=2021)

1.5.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3392-3396)

#### fn [cmp](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.cmp)<I>(self, other: I) -> [Ordering](https://doc.rust-lang.org/std/cmp/enum.Ordering.html "enum std::cmp::Ordering")where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")<Item = Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>,  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [Ord](https://doc.rust-lang.org/std/cmp/trait.Ord.html "trait std::cmp::Ord"), 

[Lexicographically](https://doc.rust-lang.org/std/cmp/trait.Ord.html#lexicographical-comparison) compares the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") with those of another.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-59)

```
use std::cmp::Ordering;

assert_eq!([1].iter().cmp([1].iter()), Ordering::Equal);
assert_eq!([1].iter().cmp([1, 2].iter()), Ordering::Less);
assert_eq!([1, 2].iter().cmp([1].iter()), Ordering::Greater);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Ause%20std%3A%3Acmp%3A%3AOrdering%3B%0A%0Aassert_eq!(%5B1%5D.iter().cmp(%5B1%5D.iter())%2C%20Ordering%3A%3AEqual)%3B%0Aassert_eq!(%5B1%5D.iter().cmp(%5B1%2C%202%5D.iter())%2C%20Ordering%3A%3ALess)%3B%0Aassert_eq!(%5B1%2C%202%5D.iter().cmp(%5B1%5D.iter())%2C%20Ordering%3A%3AGreater)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3421-3425)

#### fn [cmp\_by](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.cmp_by)<I, F>(self, other: I, cmp: F) -> [Ordering](https://doc.rust-lang.org/std/cmp/enum.Ordering.html "enum std::cmp::Ordering")where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), <I as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[Item](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.Item "type std::iter::IntoIterator::Item")) -> [Ordering](https://doc.rust-lang.org/std/cmp/enum.Ordering.html "enum std::cmp::Ordering"), 

🔬 This is a nightly-only experimental API. (`iter_order_by` [#64295](https://github.com/rust-lang/rust/issues/64295))

[Lexicographically](https://doc.rust-lang.org/std/cmp/trait.Ord.html#lexicographical-comparison) compares the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") with those of another with respect to the specified comparison function.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-60)

Basic usage:

```
#![feature(iter_order_by)]

use std::cmp::Ordering;

let xs = [1, 2, 3, 4];
let ys = [1, 4, 9, 16];

assert_eq!(xs.iter().cmp_by(&ys, |&x, &y| x.cmp(&y)), Ordering::Less);
assert_eq!(xs.iter().cmp_by(&ys, |&x, &y| (x * x).cmp(&y)), Ordering::Equal);
assert_eq!(xs.iter().cmp_by(&ys, |&x, &y| (2 * x).cmp(&y)), Ordering::Greater);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_order_by)%5D%0A%0Afn%20main()%20%7B%0Ause%20std%3A%3Acmp%3A%3AOrdering%3B%0A%0Alet%20xs%20%3D%20%5B1%2C%202%2C%203%2C%204%5D%3B%0Alet%20ys%20%3D%20%5B1%2C%204%2C%209%2C%2016%5D%3B%0A%0Aassert_eq!(xs.iter().cmp_by(%26ys%2C%20%7C%26x%2C%20%26y%7C%20x.cmp(%26y))%2C%20Ordering%3A%3ALess)%3B%0Aassert_eq!(xs.iter().cmp_by(%26ys%2C%20%7C%26x%2C%20%26y%7C%20(x%20*%20x).cmp(%26y))%2C%20Ordering%3A%3AEqual)%3B%0Aassert_eq!(xs.iter().cmp_by(%26ys%2C%20%7C%26x%2C%20%26y%7C%20(2%20*%20x).cmp(%26y))%2C%20Ordering%3A%3AGreater)%3B%0A%7D&version=nightly&edition=2021)

1.5.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3468-3472)

#### fn [partial\_cmp](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.partial_cmp)<I>(self, other: I) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<[Ordering](https://doc.rust-lang.org/std/cmp/enum.Ordering.html "enum std::cmp::Ordering")\> where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [PartialOrd](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html "trait std::cmp::PartialOrd")<<I as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[Item](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.Item "type std::iter::IntoIterator::Item")\>, 

[Lexicographically](https://doc.rust-lang.org/std/cmp/trait.Ord.html#lexicographical-comparison) compares the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") with those of another.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-61)

```
use std::cmp::Ordering;

assert_eq!([1.].iter().partial_cmp([1.].iter()), Some(Ordering::Equal));
assert_eq!([1.].iter().partial_cmp([1., 2.].iter()), Some(Ordering::Less));
assert_eq!([1., 2.].iter().partial_cmp([1.].iter()), Some(Ordering::Greater));

assert_eq!([f64::NAN].iter().partial_cmp([1.].iter()), None);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Ause%20std%3A%3Acmp%3A%3AOrdering%3B%0A%0Aassert_eq!(%5B1.%5D.iter().partial_cmp(%5B1.%5D.iter())%2C%20Some(Ordering%3A%3AEqual))%3B%0Aassert_eq!(%5B1.%5D.iter().partial_cmp(%5B1.%2C%202.%5D.iter())%2C%20Some(Ordering%3A%3ALess))%3B%0Aassert_eq!(%5B1.%2C%202.%5D.iter().partial_cmp(%5B1.%5D.iter())%2C%20Some(Ordering%3A%3AGreater))%3B%0A%0Aassert_eq!(%5Bf64%3A%3ANAN%5D.iter().partial_cmp(%5B1.%5D.iter())%2C%20None)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3506-3510)

#### fn [partial\_cmp\_by](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.partial_cmp_by)<I, F>(self, other: I, partial\_cmp: F) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<[Ordering](https://doc.rust-lang.org/std/cmp/enum.Ordering.html "enum std::cmp::Ordering")\> where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), <I as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[Item](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.Item "type std::iter::IntoIterator::Item")) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<[Ordering](https://doc.rust-lang.org/std/cmp/enum.Ordering.html "enum std::cmp::Ordering")\>, 

🔬 This is a nightly-only experimental API. (`iter_order_by` [#64295](https://github.com/rust-lang/rust/issues/64295))

[Lexicographically](https://doc.rust-lang.org/std/cmp/trait.Ord.html#lexicographical-comparison) compares the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") with those of another with respect to the specified comparison function.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-62)

Basic usage:

```
#![feature(iter_order_by)]

use std::cmp::Ordering;

let xs = [1.0, 2.0, 3.0, 4.0];
let ys = [1.0, 4.0, 9.0, 16.0];

assert_eq!(
    xs.iter().partial_cmp_by(&ys, |&x, &y| x.partial_cmp(&y)),
    Some(Ordering::Less)
);
assert_eq!(
    xs.iter().partial_cmp_by(&ys, |&x, &y| (x * x).partial_cmp(&y)),
    Some(Ordering::Equal)
);
assert_eq!(
    xs.iter().partial_cmp_by(&ys, |&x, &y| (2.0 * x).partial_cmp(&y)),
    Some(Ordering::Greater)
);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_order_by)%5D%0A%0Afn%20main()%20%7B%0Ause%20std%3A%3Acmp%3A%3AOrdering%3B%0A%0Alet%20xs%20%3D%20%5B1.0%2C%202.0%2C%203.0%2C%204.0%5D%3B%0Alet%20ys%20%3D%20%5B1.0%2C%204.0%2C%209.0%2C%2016.0%5D%3B%0A%0Aassert_eq!(%0A%20%20%20%20xs.iter().partial_cmp_by(%26ys%2C%20%7C%26x%2C%20%26y%7C%20x.partial_cmp(%26y))%2C%0A%20%20%20%20Some(Ordering%3A%3ALess)%0A)%3B%0Aassert_eq!(%0A%20%20%20%20xs.iter().partial_cmp_by(%26ys%2C%20%7C%26x%2C%20%26y%7C%20(x%20*%20x).partial_cmp(%26y))%2C%0A%20%20%20%20Some(Ordering%3A%3AEqual)%0A)%3B%0Aassert_eq!(%0A%20%20%20%20xs.iter().partial_cmp_by(%26ys%2C%20%7C%26x%2C%20%26y%7C%20(2.0%20*%20x).partial_cmp(%26y))%2C%0A%20%20%20%20Some(Ordering%3A%3AGreater)%0A)%3B%0A%7D&version=nightly&edition=2021)

1.5.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3548-3552)

#### fn [eq](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.eq)<I>(self, other: I) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [PartialEq](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html "trait std::cmp::PartialEq")<<I as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[Item](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.Item "type std::iter::IntoIterator::Item")\>, 

Determines if the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") are equal to those of another.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-63)

```
assert_eq!([1].iter().eq([1].iter()), true);
assert_eq!([1].iter().eq([1, 2].iter()), false);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Aassert_eq!(%5B1%5D.iter().eq(%5B1%5D.iter())%2C%20true)%3B%0Aassert_eq!(%5B1%5D.iter().eq(%5B1%2C%202%5D.iter())%2C%20false)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3573-3577)

#### fn [eq\_by](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.eq_by)<I, F>(self, other: I, eq: F) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), <I as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[Item](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.Item "type std::iter::IntoIterator::Item")) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html), 

🔬 This is a nightly-only experimental API. (`iter_order_by` [#64295](https://github.com/rust-lang/rust/issues/64295))

Determines if the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") are equal to those of another with respect to the specified equality function.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-64)

Basic usage:

```
#![feature(iter_order_by)]

let xs = [1, 2, 3, 4];
let ys = [1, 4, 9, 16];

assert!(xs.iter().eq_by(&ys, |&x, &y| x * x == y));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(iter_order_by)%5D%0A%0Afn%20main()%20%7B%0Alet%20xs%20%3D%20%5B1%2C%202%2C%203%2C%204%5D%3B%0Alet%20ys%20%3D%20%5B1%2C%204%2C%209%2C%2016%5D%3B%0A%0Aassert!(xs.iter().eq_by(%26ys%2C%20%7C%26x%2C%20%26y%7C%20x%20*%20x%20%3D%3D%20y))%3B%0A%7D&version=nightly&edition=2021)

1.5.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3608-3612)

#### fn [ne](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.ne)<I>(self, other: I) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [PartialEq](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html "trait std::cmp::PartialEq")<<I as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[Item](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.Item "type std::iter::IntoIterator::Item")\>, 

Determines if the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") are unequal to those of another.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-65)

```
assert_eq!([1].iter().ne([1].iter()), false);
assert_eq!([1].iter().ne([1, 2].iter()), true);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Aassert_eq!(%5B1%5D.iter().ne(%5B1%5D.iter())%2C%20false)%3B%0Aassert_eq!(%5B1%5D.iter().ne(%5B1%2C%202%5D.iter())%2C%20true)%3B%0A%7D&edition=2021)

1.5.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3629-3633)

#### fn [lt](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.lt)<I>(self, other: I) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [PartialOrd](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html "trait std::cmp::PartialOrd")<<I as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[Item](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.Item "type std::iter::IntoIterator::Item")\>, 

Determines if the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") are [lexicographically](https://doc.rust-lang.org/std/cmp/trait.Ord.html#lexicographical-comparison) less than those of another.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-66)

```
assert_eq!([1].iter().lt([1].iter()), false);
assert_eq!([1].iter().lt([1, 2].iter()), true);
assert_eq!([1, 2].iter().lt([1].iter()), false);
assert_eq!([1, 2].iter().lt([1, 2].iter()), false);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Aassert_eq!(%5B1%5D.iter().lt(%5B1%5D.iter())%2C%20false)%3B%0Aassert_eq!(%5B1%5D.iter().lt(%5B1%2C%202%5D.iter())%2C%20true)%3B%0Aassert_eq!(%5B1%2C%202%5D.iter().lt(%5B1%5D.iter())%2C%20false)%3B%0Aassert_eq!(%5B1%2C%202%5D.iter().lt(%5B1%2C%202%5D.iter())%2C%20false)%3B%0A%7D&edition=2021)

1.5.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3650-3654)

#### fn [le](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.le)<I>(self, other: I) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [PartialOrd](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html "trait std::cmp::PartialOrd")<<I as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[Item](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.Item "type std::iter::IntoIterator::Item")\>, 

Determines if the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") are [lexicographically](https://doc.rust-lang.org/std/cmp/trait.Ord.html#lexicographical-comparison) less or equal to those of another.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-67)

```
assert_eq!([1].iter().le([1].iter()), true);
assert_eq!([1].iter().le([1, 2].iter()), true);
assert_eq!([1, 2].iter().le([1].iter()), false);
assert_eq!([1, 2].iter().le([1, 2].iter()), true);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Aassert_eq!(%5B1%5D.iter().le(%5B1%5D.iter())%2C%20true)%3B%0Aassert_eq!(%5B1%5D.iter().le(%5B1%2C%202%5D.iter())%2C%20true)%3B%0Aassert_eq!(%5B1%2C%202%5D.iter().le(%5B1%5D.iter())%2C%20false)%3B%0Aassert_eq!(%5B1%2C%202%5D.iter().le(%5B1%2C%202%5D.iter())%2C%20true)%3B%0A%7D&edition=2021)

1.5.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3671-3675)

#### fn [gt](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.gt)<I>(self, other: I) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [PartialOrd](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html "trait std::cmp::PartialOrd")<<I as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[Item](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.Item "type std::iter::IntoIterator::Item")\>, 

Determines if the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") are [lexicographically](https://doc.rust-lang.org/std/cmp/trait.Ord.html#lexicographical-comparison) greater than those of another.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-68)

```
assert_eq!([1].iter().gt([1].iter()), false);
assert_eq!([1].iter().gt([1, 2].iter()), false);
assert_eq!([1, 2].iter().gt([1].iter()), true);
assert_eq!([1, 2].iter().gt([1, 2].iter()), false);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Aassert_eq!(%5B1%5D.iter().gt(%5B1%5D.iter())%2C%20false)%3B%0Aassert_eq!(%5B1%5D.iter().gt(%5B1%2C%202%5D.iter())%2C%20false)%3B%0Aassert_eq!(%5B1%2C%202%5D.iter().gt(%5B1%5D.iter())%2C%20true)%3B%0Aassert_eq!(%5B1%2C%202%5D.iter().gt(%5B1%2C%202%5D.iter())%2C%20false)%3B%0A%7D&edition=2021)

1.5.0 · [source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3692-3696)

#### fn [ge](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.ge)<I>(self, other: I) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    I: [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator"),  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [PartialOrd](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html "trait std::cmp::PartialOrd")<<I as [IntoIterator](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html "trait std::iter::IntoIterator")\>::[Item](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.Item "type std::iter::IntoIterator::Item")\>, 

Determines if the elements of this [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html "Iterator") are [lexicographically](https://doc.rust-lang.org/std/cmp/trait.Ord.html#lexicographical-comparison) greater than or equal to those of another.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-69)

```
assert_eq!([1].iter().ge([1].iter()), true);
assert_eq!([1].iter().ge([1, 2].iter()), false);
assert_eq!([1, 2].iter().ge([1].iter()), true);
assert_eq!([1, 2].iter().ge([1, 2].iter()), true);
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Aassert_eq!(%5B1%5D.iter().ge(%5B1%5D.iter())%2C%20true)%3B%0Aassert_eq!(%5B1%5D.iter().ge(%5B1%2C%202%5D.iter())%2C%20false)%3B%0Aassert_eq!(%5B1%2C%202%5D.iter().ge(%5B1%5D.iter())%2C%20true)%3B%0Aassert_eq!(%5B1%2C%202%5D.iter().ge(%5B1%2C%202%5D.iter())%2C%20true)%3B%0A%7D&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3723-3726)

#### fn [is\_sorted](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.is_sorted)(self) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"): [PartialOrd](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html "trait std::cmp::PartialOrd")<Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")\>, 

🔬 This is a nightly-only experimental API. (`is_sorted` [#53485](https://github.com/rust-lang/rust/issues/53485))

Checks if the elements of this iterator are sorted.

That is, for each element `a` and its following element `b`, `a <= b` must hold. If the iterator yields exactly zero or one element, `true` is returned.

Note that if `Self::Item` is only `PartialOrd`, but not `Ord`, the above definition implies that this function returns `false` if any two consecutive items are not comparable.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-70)

```
#![feature(is_sorted)]

assert!([1, 2, 2, 9].iter().is_sorted());
assert!(![1, 3, 2, 4].iter().is_sorted());
assert!([0].iter().is_sorted());
assert!(std::iter::empty::<i32>().is_sorted());
assert!(![0.0, 1.0, f32::NAN].iter().is_sorted());
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(is_sorted)%5D%0A%0Afn%20main()%20%7B%0Aassert!(%5B1%2C%202%2C%202%2C%209%5D.iter().is_sorted())%3B%0Aassert!(!%5B1%2C%203%2C%202%2C%204%5D.iter().is_sorted())%3B%0Aassert!(%5B0%5D.iter().is_sorted())%3B%0Aassert!(std%3A%3Aiter%3A%3Aempty%3A%3A%3Ci32%3E().is_sorted())%3B%0Aassert!(!%5B0.0%2C%201.0%2C%20f32%3A%3ANAN%5D.iter().is_sorted())%3B%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3751-3754)

#### fn [is\_sorted\_by](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.is_sorted_by)<F>(self, compare: F) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(&Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item"), &Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> [Option](https://doc.rust-lang.org/std/option/enum.Option.html "enum std::option::Option")<[Ordering](https://doc.rust-lang.org/std/cmp/enum.Ordering.html "enum std::cmp::Ordering")\>, 

🔬 This is a nightly-only experimental API. (`is_sorted` [#53485](https://github.com/rust-lang/rust/issues/53485))

Checks if the elements of this iterator are sorted using the given comparator function.

Instead of using `PartialOrd::partial_cmp`, this function uses the given `compare` function to determine the ordering of two elements. Apart from that, it’s equivalent to [`is_sorted`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.is_sorted); see its documentation for more information.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-71)

```
#![feature(is_sorted)]

assert!([1, 2, 2, 9].iter().is_sorted_by(|a, b| a.partial_cmp(b)));
assert!(![1, 3, 2, 4].iter().is_sorted_by(|a, b| a.partial_cmp(b)));
assert!([0].iter().is_sorted_by(|a, b| a.partial_cmp(b)));
assert!(std::iter::empty::<i32>().is_sorted_by(|a, b| a.partial_cmp(b)));
assert!(![0.0, 1.0, f32::NAN].iter().is_sorted_by(|a, b| a.partial_cmp(b)));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(is_sorted)%5D%0A%0Afn%20main()%20%7B%0Aassert!(%5B1%2C%202%2C%202%2C%209%5D.iter().is_sorted_by(%7Ca%2C%20b%7C%20a.partial_cmp(b)))%3B%0Aassert!(!%5B1%2C%203%2C%202%2C%204%5D.iter().is_sorted_by(%7Ca%2C%20b%7C%20a.partial_cmp(b)))%3B%0Aassert!(%5B0%5D.iter().is_sorted_by(%7Ca%2C%20b%7C%20a.partial_cmp(b)))%3B%0Aassert!(std%3A%3Aiter%3A%3Aempty%3A%3A%3Ci32%3E().is_sorted_by(%7Ca%2C%20b%7C%20a.partial_cmp(b)))%3B%0Aassert!(!%5B0.0%2C%201.0%2C%20f32%3A%3ANAN%5D.iter().is_sorted_by(%7Ca%2C%20b%7C%20a.partial_cmp(b)))%3B%0A%7D&version=nightly&edition=2021)

[source](https://doc.rust-lang.org/src/core/iter/traits/iterator.rs.html#3797-3801)

#### fn [is\_sorted\_by\_key](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.is_sorted_by_key)<F, K>(self, f: F) -> [bool](https://doc.rust-lang.org/std/primitive.bool.html)where  
    F: [FnMut](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut")(Self::[Item](https://doc.rust-lang.org/std/iter/trait.Iterator.html#associatedtype.Item "type std::iter::Iterator::Item")) -> K,  
    K: [PartialOrd](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html "trait std::cmp::PartialOrd")<K>, 

🔬 This is a nightly-only experimental API. (`is_sorted` [#53485](https://github.com/rust-lang/rust/issues/53485))

Checks if the elements of this iterator are sorted using the given key extraction function.

Instead of comparing the iterator’s elements directly, this function compares the keys of the elements, as determined by `f`. Apart from that, it’s equivalent to [`is_sorted`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.is_sorted); see its documentation for more information.

##### [Examples](https://doc.rust-lang.org/std/iter/trait.Iterator.html#examples-72)

```
#![feature(is_sorted)]

assert!(["c", "bb", "aaa"].iter().is_sorted_by_key(|s| s.len()));
assert!(![-2i32, -1, 0, 3].iter().is_sorted_by_key(|n| n.abs()));
```

[](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0A%23!%5Bfeature(is_sorted)%5D%0A%0Afn%20main()%20%7B%0Aassert!(%5B%22c%22%2C%20%22bb%22%2C%20%22aaa%22%5D.iter().is_sorted_by_key(%7Cs%7C%20s.len()))%3B%0Aassert!(!%5B-2i32%2C%20-1%2C%200%2C%203%5D.iter().is_sorted_by_key(%7Cn%7C%20n.abs()))%3B%0A%7D&version=nightly&edition=2021)