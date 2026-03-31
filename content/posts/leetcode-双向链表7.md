---
title: "Leetcode 双向链表7"
date: 2025-06-16T22:16:50+08:00
tags: ["leetcode", "双向链表"]
draft: false
---

## 设计一个验证系统

你需要设计一个包含验证码的验证系统。每一次验证中，用户会收到一个新的验证码，这个验证码在 currentTime 时刻之后 timeToLive 秒过期。如果验证码被更新了，那么它会在 currentTime （可能与之前的 currentTime 不同）时刻延长 timeToLive 秒。

请你实现 AuthenticationManager 类：

AuthenticationManager(int timeToLive) 构造 AuthenticationManager 并设置 timeToLive 参数。
generate(string tokenId, int currentTime) 给定 tokenId ，在当前时间 currentTime 生成一个新的验证码。
renew(string tokenId, int currentTime) 将给定 tokenId 且 未过期 的验证码在 currentTime 时刻更新。如果给定 tokenId 对应的验证码不存在或已过期，请你忽略该操作，不会有任何更新操作发生。
countUnexpiredTokens(int currentTime) 请返回在给定 currentTime 时刻，未过期 的验证码数目。
如果一个验证码在时刻 t 过期，且另一个操作恰好在时刻 t 发生（renew 或者 countUnexpiredTokens 操作），过期事件 优先于 其他操作。


输入：
["AuthenticationManager", "renew", "generate", "countUnexpiredTokens", "generate", "renew", "renew", "countUnexpiredTokens"]
[[5], ["aaa", 1], ["aaa", 2], [6], ["bbb", 7], ["aaa", 8], ["bbb", 10], [15]]
输出：
[null, null, null, 1, null, null, null, 0]

解释：
AuthenticationManager authenticationManager = new AuthenticationManager(5); // 构造 AuthenticationManager ，设置 timeToLive = 5 秒。
authenticationManager.renew("aaa", 1); // 时刻 1 时，没有验证码的 tokenId 为 "aaa" ，没有验证码被更新。
authenticationManager.generate("aaa", 2); // 时刻 2 时，生成一个 tokenId 为 "aaa" 的新验证码。
authenticationManager.countUnexpiredTokens(6); // 时刻 6 时，只有 tokenId 为 "aaa" 的验证码未过期，所以返回 1 。
authenticationManager.generate("bbb", 7); // 时刻 7 时，生成一个 tokenId 为 "bbb" 的新验证码。
authenticationManager.renew("aaa", 8); // tokenId 为 "aaa" 的验证码在时刻 7 过期，且 8 >= 7 ，所以时刻 8 的renew 操作被忽略，没有验证码被更新。
authenticationManager.renew("bbb", 10); // tokenId 为 "bbb" 的验证码在时刻 10 没有过期，所以 renew 操作会执行，该 token 将在时刻 15 过期。
authenticationManager.countUnexpiredTokens(15); // tokenId 为 "bbb" 的验证码在时刻 15 过期，tokenId 为 "aaa" 的验证码在时刻 7 过期，所有验证码均已过期，所以返回 0 。


```
use std::collections::HashMap;
struct AuthenticationManager {
    tab: HashMap<String, i32>, // 存储 token_id -> 过期时间 的映射
    ttl: i32,// Time To Live，令牌的有效时长
}

impl AuthenticationManager {

    // 创建新的认证管理器实例    初始化空的哈希表，设置令牌有效时长
    fn new(timeToLive: i32) -> Self {
        Self {tab: HashMap::new(), ttl: timeToLive}
    }

    // 生成新的认证令牌
    // 计算过期时间 = 当前时间 + TTL
    // 如果令牌已存在，会覆盖原有的过期时间
    // or_insert(0) 返回值的可变引用，然后赋值为新的过期时间
    fn generate(&mut self, token_id: String, current_time: i32) {
        *self.tab.entry(token_id).or_insert(0) = current_time + self.ttl;
    }

    // 续期未过期的令牌
    // 关键逻辑：只有令牌未过期（过期时间 > 当前时间）时才续期
    // 续期后将过期时间更新为 current_time + TTL
    // 如果令牌不存在或已过期，则不进行任何操作
    fn renew(&mut self, token_id: String, current_time: i32) {
        if let Some(t) = self.tab.get_mut(&token_id) {
            if *t > current_time {
                *t = current_time + self.ttl;
            }
        }
    }


    // 统计所有未过期的令牌数量
    // 使用 fold 累加器遍历所有令牌
    // (t > current_time) as i32 将布尔值转换为整数（true=1, false=0）
    // 返回未过期令牌的总数
    fn count_unexpired_tokens(&self, current_time: i32) -> i32 {
        self.tab.iter().fold(0, |ans, (_, &t)| {
            ans + (t > current_time) as i32
        })
    }
}

```


设计特点

惰性清理：不会主动删除过期令牌，只在续期时检查，在计数时筛选
时间点判断：所有操作都基于给定的 current_time 参数
简单高效：使用 HashMap 实现 O(1) 的读写操作
