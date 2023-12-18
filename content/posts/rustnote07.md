---
title: "Rust Note 07"
date: 2023-05-19T21:35:43+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---
## 加一
给定一个由 整数 组成的 非空 数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。


### C 解法：
```
int* plusOne(int* digits, int digitsSize, int* returnSize){
    for(int i = digitsSize - 1; i >= 0; --i){
        digits[i] = digits[i] + 1;//最后元素+1判断是不是10
        //如果当前元素不为10，直接返回数组
        if(digits[i] != 10){
            *returnSize = digitsSize;
            return digits;
        }
        //第一个元素不为10，后面元素均为10的情况
        if(digits[i] == 10)
            digits[i] = 0;
    }
    //元素全为9开辟新数组
    int* ans = malloc(sizeof(int) * (digitsSize + 1));
    memset(ans, 0, sizeof(int) * (digitsSize + 1));//全部置0
    ans[0] = 1;
    *returnSize = digitsSize + 1;
    return ans;
}
```

### Rust 解法：
```
impl Solution {
    pub fn plus_one(digits: Vec<i32>) -> Vec<i32> {
        match digits.iter().enumerate().rev().skip_while(|(i,&x)|9==x).next() {
            Some((i,&a))=>
                    [digits[..i].iter().map(|x| x.clone()).collect::<Vec<i32>>().as_slice()
                        , vec![digits[i] + 1].as_slice(), vec![0; digits.len() - (i + 1)].as_slice()].concat(),
            None=> [vec![1,].as_slice(),vec![0;digits.len()].as_slice()].concat()
        }
    }
}
```