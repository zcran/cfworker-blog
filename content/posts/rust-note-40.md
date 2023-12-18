---
title: "Rust Note 40"
date: 2023-06-10T19:51:15+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## Fizz Buzz

给你一个整数 n ，找出从 1 到 n 各个整数的 Fizz Buzz 表示，并用字符串数组 answer（下标从 1 开始）返回结果，其中：

answer[i] == "FizzBuzz" 如果 i 同时是 3 和 5 的倍数。
answer[i] == "Fizz" 如果 i 是 3 的倍数。
answer[i] == "Buzz" 如果 i 是 5 的倍数。
answer[i] == i （以字符串形式）如果上述条件全不满足。

### C 解法：
```
class Solution {
public:
    vector<string> fizzBuzz(int n) {
        vector<string> answer;
        for (int i = 1; i <= n; i++) {
            string curr;
            if (i % 3 == 0) {
                curr　+= "Fizz";
            }
            if (i % 5 == 0) {
                curr += "Buzz";
            }
            if (curr.size() == 0) {
                curr += to_string(i);
            }            
            answer.emplace_back(curr);
        }
        return answer;
    }
};
```


### Rust 解法：
```
impl Solution {
    pub fn fizz_buzz(n: i32) -> Vec<String> {
        (1..n+1).map(|i|
            match (0==i%3,0==i%5) {
                (true,true)=>"FizzBuzz".to_string(),
                (true,false)=>"Fizz".to_string(),
                (false,true)=>"Buzz".to_string(),
                _=>i.to_string()
            }
        ).collect()
    }
}
```