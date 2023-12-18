---
title: "Rust Note 13"
date: 2023-05-22T20:28:46+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 整数反转

给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 [−231,  231 − 1] ，就返回 0。

假设环境不允许存储 64 位整数（有符号或无符号）。



### C 解法：
```
class Solution {
public:
    int reverse(int x) {
        int res=0;
        while(x){
            if(x>0 && res>(INT_MAX-x%10)/10) return 0;
            if(x<0 && res<(INT_MIN-x%10)/10) return 0;
            res = res*10+x%10;
            x/=10;
        }
        return res;
    }
};
```


### Rust 解法：
```

impl Solution {
    pub fn reverse(x: i32) -> i32 {
     
       x.abs()
        .to_string()
        .chars().rev()
        .collect::<String>()
        .parse::<i32>()
        .unwrap_or(0) * x.signum()
    }
}
```

### Rust abs()函数
pub const fn abs(self) -> i32
计算 self 的绝对值。

溢出行为

的绝对值 i32::MIN 不能表示为 i32, 并尝试计算它会导致溢出。 这意味着在这种情况下，处于调试模式的代码将触发 panic，并且优化后的代码将返回

i32::MIN 没有 panic。

### Rust chars结构体
pub struct Chars<'a> { /* fields omitted */ }
字符串切片的 char 上的迭代器。

该结构体是通过 str 上的 chars 方法创建的。

pub fn as_str(&self) -> &'a str
将底层数据视为原始数据的子切片。

它具有与原始切片相同的生命周期，因此迭代器可以在存在时继续使用。

Examples：
```
let mut chars = "abc".chars();

assert_eq!(chars.as_str(), "abc");
chars.next();
assert_eq!(chars.as_str(), "bc");
chars.next();
chars.next();
assert_eq!(chars.as_str(), "");
```

#### 迭代器trait rev()
fn rev(self) -> Rev<Self>ⓘ
where
    Self: DoubleEndedIterator, 
反转迭代器的方向。

通常，迭代器从左到右进行迭代。 使用 rev() 之后，迭代器将改为从右向左进行迭代。

仅在迭代器具有结束符的情况下才有可能，因此 rev() 仅适用于 DoubleEndedIterator。

Examples：
```
let a = [1, 2, 3];

let mut iter = a.iter().rev();

assert_eq!(iter.next(), Some(&3));
assert_eq!(iter.next(), Some(&2));
assert_eq!(iter.next(), Some(&1));

assert_eq!(iter.next(), None);
```

source: 
```
#[inline]
    #[doc(alias = "reverse")]
    #[stable(feature = "rust1", since = "1.0.0")]
    fn rev(self) -> Rev<Self>
    where
        Self: Sized + DoubleEndedIterator,
    {
        Rev::new(self)
    }
```

#### 迭代器trait collect()
fn collect<B>(self) -> B
where
    B: FromIterator<Self::Item>, 
将迭代器转换为集合。

collect() 可以将任何可迭代的东西变成一个相关的集合。 这是在各种上下文中使用的标准库中功能更强大的方法之一。

使用 collect() 的最基本模式是将一个集合转换为另一个集合。 您进行了一个收集，在其上调用 iter，进行了一堆转换，最后添加 collect()。

collect() 还可以创建非典型集合类型的实例。 例如，可以从 char 构建一个 String，并且可以将 Result<T, E> 项的迭代器收集到 Result<Collection<T>, E> 中。

由于 collect() 非常通用，因此可能导致类型推断问题。 因此，collect() 是少数几次您会看到被亲切地称为 ‘turbofish’: ::<> 的语法之一。 这有助于推理算法特别了解您要收集到的集合。

Examples：
基本用法：
```
let a = [1, 2, 3];

let doubled: Vec<i32> = a.iter()
                         .map(|&x| x * 2)
                         .collect();

assert_eq!(vec![2, 4, 6], doubled);
```

请注意，我们需要在左侧使用 : Vec<i32>。这是因为我们可以代替收集到例如 VecDeque<T> 中：
```
use std::collections::VecDeque;

let a = [1, 2, 3];

let doubled: VecDeque<i32> = a.iter().map(|&x| x * 2).collect();

assert_eq!(2, doubled[0]);
assert_eq!(4, doubled[1]);
assert_eq!(6, doubled[2]);
```

使用 ‘turbofish’ 而不是注解 doubled：
```
let a = [1, 2, 3];

let doubled = a.iter().map(|x| x * 2).collect::<Vec<i32>>();

assert_eq!(vec![2, 4, 6], doubled);
```

因为 collect() 只关心您要收集的内容，所以您仍然可以将局部类型提示 _ 与 turbfish 一起使用：
```
let a = [1, 2, 3];

let doubled = a.iter().map(|x| x * 2).collect::<Vec<_>>();

assert_eq!(vec![2, 4, 6], doubled);
```

使用 collect() 生成 String：
```
let chars = ['g', 'd', 'k', 'k', 'n'];

let hello: String = chars.iter()
    .map(|&x| x as u8)
    .map(|x| (x + 1) as char)
    .collect();

assert_eq!("hello", hello);
```

如果您有 Result<T, E>，您可以使用 collect() 来查看它们是否失败：
```
let results = [Ok(1), Err("nope"), Ok(3), Err("bad")];

let result: Result<Vec<_>, &str> = results.iter().cloned().collect();

// 给我们第一个错误
assert_eq!(Err("nope"), result);

let results = [Ok(1), Ok(3)];

let result: Result<Vec<_>, &str> = results.iter().cloned().collect();

// 给我们答案列表
assert_eq!(Ok(vec![1, 3]), result);
```


### Rust parse函数
pub fn parse<F>(&self) -> Result<F, <F as FromStr>::Err>
where
    F: FromStr, 
将此字符串切片解析为另一种类型。

由于 parse 非常通用，因此可能导致类型推断问题。 因此，parse 是少数几次您会看到被亲切地称为 ‘turbofish’: ::<> 的语法之一。

这可以帮助推理算法特别了解您要解析为哪种类型。

parse 可以解析为任何实现 FromStr trait 的类型。

Examples：
基本用法
```
let four: u32 = "4".parse().unwrap();
assert_eq!(4, four);
```

使用 ‘turbofish’ 而不是注解 four：
```
let four = "4".parse::<u32>();
assert_eq!(Ok(4), four);
```

无法解析：
```
let nope = "j".parse::<u32>();
assert!(nope.is_err());
```

### Rust signum函数
pub const fn signum(self) -> i32
返回一个表示 self 的符号的数字。

0 如果数字为零
1 如果数字是正数
-1 如果数字是负数

Examples:
```
assert_eq!(10i32.signum(), 1);
assert_eq!(0i32.signum(), 0);
assert_eq!((-10i32).signum(), -1);
```