---
title: "leetcode-设计26"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 简易银行系统

你的任务是为一个很受欢迎的银行设计一款程序，以自动化执行所有传入的交易（转账，存款和取款）。银行共有 n 个账户，编号从 1 到 n 。每个账号的初始余额存储在一个下标从 0 开始的整数数组 balance 中，其中第 (i + 1) 个账户的初始余额是 balance[i] 。

请你执行所有 有效的 交易。如果满足下面全部条件，则交易 有效 ：

· 指定的账户编号在 1 和 n 之间，且
· 取款或者转账需要的钱的总数 小于或者等于 账户余额。

实现 Bank 类：

· Bank(long[] balance) 使用下标从 0 开始的整数数组 balance 初始化该对象。
· boolean transfer(int account1, int account2, long money) 从编号为 account1 的账户向编号为 account2 的账户转帐 money 美元。如果交易成功，返回 true ，否则，返回 false 。
· boolean deposit(int account, long money) 向编号为 account 的账户存款 money 美元。如果交易成功，返回 true ；否则，返回 false 。
· boolean withdraw(int account, long money) 从编号为 account 的账户取款 money 美元。如果交易成功，返回 true ；否则，返回 false 。


```
/// 银行账户交易系统
///
/// 支持转账、存款、取款操作，所有操作 O(1)
/// 时间复杂度: O(1), 空间复杂度: O(n)
struct Bank {
    balance: Vec<i64>, // 账户余额，索引 0 对应账户 1
}

impl Bank {
    /// 初始化银行账户
    fn new(balance: Vec<i64>) -> Self {
        Bank { balance }
    }

    /// 转账：从 account1 转 money 到 account2
    fn transfer(&mut self, account1: i32, account2: i32, money: i64) -> bool {
        let (from, to) = (account1 as usize - 1, account2 as usize - 1);

        // 验证账户有效且余额充足
        if from >= self.balance.len()
            || to >= self.balance.len()
            || self.balance[from] < money {
            return false;
        }

        self.balance[from] -= money;
        self.balance[to] += money;
        true
    }

    /// 存款：向指定账户存入 money
    fn deposit(&mut self, account: i32, money: i64) -> bool {
        let idx = account as usize - 1;
        if idx >= self.balance.len() {
            return false;
        }
        self.balance[idx] += money;
        true
    }

    /// 取款：从指定账户取出 money
    fn withdraw(&mut self, account: i32, money: i64) -> bool {
        let idx = account as usize - 1;
        if idx >= self.balance.len() || self.balance[idx] < money {
            return false;
        }
        self.balance[idx] -= money;
        true
    }
}
```
