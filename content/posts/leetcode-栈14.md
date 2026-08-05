---
title: "leetcode-栈14"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 标签验证器

给定一个表示代码片段的字符串，你需要实现一个验证器来解析这段代码，并返回它是否合法。合法的代码片段需要遵守以下的所有规则：

1. 代码必须被合法的闭合标签包围。否则，代码是无效的。
2. 闭合标签（不一定合法）要严格符合格式：<TAG_NAME>TAG_CONTENT</TAG_NAME>。其中，<TAG_NAME>是起始标签，</TAG_NAME>是结束标签。起始和结束标签中的 TAG_NAME 应当相同。当且仅当 TAG_NAME 和 TAG_CONTENT 都是合法的，闭合标签才是合法的。
3. 合法的 TAG_NAME 仅含有大写字母，长度在范围 [1,9] 之间。否则，该 TAG_NAME 是不合法的。
4. 合法的 TAG_CONTENT 可以包含其他合法的闭合标签，cdata （请参考规则7）和任意字符（注意参考规则1）除了不匹配的<、不匹配的起始和结束标签、不匹配的或带有不合法 TAG_NAME 的闭合标签。否则，TAG_CONTENT 是不合法的。
5. 一个起始标签，如果没有具有相同 TAG_NAME 的结束标签与之匹配，是不合法的。反之亦然。不过，你也需要考虑标签嵌套的问题。
6. 一个<，如果你找不到一个后续的>与之匹配，是不合法的。并且当你找到一个<或</时，所有直到下一个>的前的字符，都应当被解析为 TAG_NAME（不一定合法）。
7. cdata 有如下格式：<![CDATA[CDATA_CONTENT]]>。CDATA_CONTENT 的范围被定义成 <![CDATA[ 和后续的第一个 ]]>之间的字符。
8. CDATA_CONTENT 可以包含任意字符。cdata 的功能是阻止验证器解析CDATA_CONTENT，所以即使其中有一些字符可以被解析为标签（无论合法还是不合法），也应该将它们视为常规字符。


```
//! HTML/XML 标签验证器
//!
//! 使用状态机模式验证代码片段是否符合标签语法规则。
//! 支持普通标签、CDATA 块和标签嵌套验证。


impl Solution {
    /// 验证代码片段是否合法
    ///
    /// # 规则
    /// 1. 代码必须被合法的闭合标签包围
    /// 2. 标签名必须是大写字母，长度 1-9
    /// 3. 支持 CDATA 块：`<![CDATA[内容]]>`
    /// 4. 标签必须正确匹配和嵌套
    pub fn is_valid(code: String) -> bool {
        code.chars()
            .try_fold(Validator::new(), |mut validator, c| {
                validator.handle(c).map(|_| validator)
            })
            .and_then(|validator| {
                if validator.is_end() {
                    Ok(())
                } else {
                    Err("文件未正确结束：根标签未闭合")
                }
            })
            .is_ok()
    }
}

/// 解析结果类型：成功或错误信息
type Result = std::result::Result<(), &'static str>;

/// 解析器状态机
#[derive(Debug)]
enum State {
    /// 初始状态，等待开始标签
    Init,
    /// 正在解析标签名
    /// - `cache`: 已收集的标签名
    /// - `is_close`: 是否为结束标签
    TagName { cache: String, is_close: bool },
    /// 标签内容区域
    TagContent,
    /// 正在解析 CDATA 起始标记 `<![CDATA[`
    CDataTag { cache: String },
    /// CDATA 内容区域
    /// - `prefix`: (是否遇到第一个 `]`, 是否遇到第二个 `]`)
    CDataContent { prefix: (bool, bool) },
    /// 解析完成
    End,
}

/// 验证器，维护解析状态和标签栈
struct Validator {
    state: State,
    stack: Vec<String>, // 标签栈，用于匹配起始/结束标签
}

impl Validator {
    /// 创建新的验证器
    fn new() -> Self {
        Self {
            state: State::Init,
            stack: Vec::new(),
        }
    }

    /// 检查解析是否已完成
    fn is_end(&self) -> bool {
        matches!(self.state, State::End)
    }

    /// 处理单个字符
    fn handle(&mut self, c: char) -> Result {
        match &self.state {
            State::Init => self.handle_init(c),
            State::TagName { .. } => self.handle_tag_name(c),
            State::TagContent => self.handle_tag_content(c),
            State::CDataTag { .. } => self.handle_cdata_tag(c),
            State::CDataContent { .. } => self.handle_cdata_content(c),
            State::End => self.handle_end(c),
        }
    }

    // ===== 各状态的处理逻辑 =====

    /// 初始状态：只接受 `<` 字符
    fn handle_init(&mut self, c: char) -> Result {
        match (&self.state, c) {
            (State::Init, '<') => {
                self.state = State::TagName {
                    cache: String::new(),
                    is_close: false,
                };
                Ok(())
            }
            (State::Init, _) => Err("期望 '<' 开始标签"),
            _ => panic!("状态错误：期望 Init"),
        }
    }

    /// 标签名状态：收集标签名
    fn handle_tag_name(&mut self, c: char) -> Result {
        match &mut self.state {
            State::TagName { cache, is_close } => {
                match (c, cache.len(), *is_close) {
                    // 大写字母：添加到标签名缓存（长度不超过 9）
                    (c @ 'A'..='Z', 0..=8, _) => {
                        cache.push(c);
                        Ok(())
                    }

                    // '>' 结束标签名：验证标签名合法性
                    ('>', 1..=9, false) => {
                        // 起始标签：压栈
                        self.stack.push(cache.clone());
                        self.state = State::TagContent;
                        Ok(())
                    }
                    ('>', 1..=9, true) => {
                        // 结束标签：匹配栈顶
                        match self.stack.pop() {
                            Some(tag) if tag == *cache => {
                                // 栈为空表示所有标签都已闭合
                                self.state = if self.stack.is_empty() {
                                    State::End
                                } else {
                                    State::TagContent
                                };
                                Ok(())
                            }
                            _ => Err("结束标签不匹配"),
                        }
                    }

                    // '/' 起始标签的结束标记（如 `<TAG/>` 不支持自闭合）
                    ('/', 0, false) => {
                        *is_close = true;
                        Ok(())
                    }

                    // '!' 后跟 CDATA：仅当已进入标签内容（不在根标签外）
                    ('!', 0, false) if !self.stack.is_empty() => {
                        self.state = State::CDataTag {
                            cache: String::new(),
                        };
                        Ok(())
                    }

                    // 其他字符非法
                    _ => Err("标签名中包含非法字符"),
                }
            }
            _ => panic!("状态错误：期望 TagName"),
        }
    }

    /// 标签内容状态：普通文本或子标签
    fn handle_tag_content(&mut self, c: char) -> Result {
        match (&self.state, c) {
            (State::TagContent, '<') => {
                // 遇到 '<' 开始解析新标签
                self.state = State::TagName {
                    cache: String::new(),
                    is_close: false,
                };
                Ok(())
            }
            (State::TagContent, _) => Ok(()), // 普通字符，直接忽略
            _ => panic!("状态错误：期望 TagContent"),
        }
    }

    /// CDATA 标签状态：匹配 `<![CDATA[`
    fn handle_cdata_tag(&mut self, c: char) -> Result {
        match &mut self.state {
            State::CDataTag { cache } => {
                match (c, cache.len()) {
                    // 收集 CDATA 前缀字符
                    (_, 0..=6) => {
                        cache.push(c);
                        // 检查是否匹配完整的 CDATA 起始标记
                        if cache.as_str() == "[CDATA[" {
                            self.state = State::CDataContent {
                                prefix: (false, false),
                            };
                            Ok(())
                        } else if "[CDATA[".starts_with(cache.as_str()) {
                            Ok(())
                        } else {
                            Err("CDATA 起始标记格式错误")
                        }
                    }
                    _ => Err("CDATA 标记长度超限"),
                }
            }
            _ => panic!("状态错误：期望 CDataTag"),
        }
    }

    /// CDATA 内容状态：查找 `]]>` 结束符
    fn handle_cdata_content(&mut self, c: char) -> Result {
        match &mut self.state {
            State::CDataContent { prefix } => {
                let (first, second) = *prefix;
                match (first, second, c) {
                    // 找到完整的 `]]>` 结束符，回到标签内容
                    (true, true, '>') => {
                        self.state = State::TagContent;
                        Ok(())
                    }
                    // 连续 ']' 序列，保留状态
                    (true, true, ']') => Ok(()),
                    // 遇到其他字符，重置前缀匹配
                    (true, true, _) => {
                        *prefix = (false, false);
                        Ok(())
                    }
                    // 第一个 ']'
                    (true, false, ']') => {
                        *prefix = (true, true);
                        Ok(())
                    }
                    // 第二个 ']'
                    (false, false, ']') => {
                        *prefix = (true, false);
                        Ok(())
                    }
                    // 其他字符
                    _ => Ok(()),
                }
            }
            _ => panic!("状态错误：期望 CDataContent"),
        }
    }

    /// 结束状态：不应再接收任何字符
    fn handle_end(&mut self, _c: char) -> Result {
        match self.state {
            State::End => Err("代码已结束，不应再有字符"),
            _ => panic!("状态错误：期望 End"),
        }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_validation() {
        let cases = vec![
            // 合法用例
            (true, "<DIV>This is the first line <![CDATA[<div>]]></DIV>"),
            (true, "<DIV>>>  ![cdata[]] <![CDATA[<div>]>]]>]]>>]</DIV>"),
            (true, "<TRUE><![CDATA[wahaha]]]><![CDATA[]> wahaha]]></TRUE>"),

            // 非法用例
            (false, "<DIV>>>  ![cdata[]] </![CDATA[<div>]>]]>]]>>]</DIV>"),
            (false, "<A>  <B> </A>   </B>"),
            (false, "<DIV>  div tag is not closed  <DIV>"),
            (false, "<DIV>  unmatched <  </DIV>"),
            (false, "<DIV> closed tags with invalid tag name  <b>123</b> </DIV>"),
            (false, "<DIV> unmatched tags with invalid tag name  </1234567890> and <CDATA[[]]>  </DIV>"),
            (false, "<DIV>  unmatched start tag <B>  and unmatched end tag </C>  </DIV>"),
            (false, "<A><![CDATA[</A>]]123></A>"),
        ];

        for (expected, input) in cases {
            assert_eq!(expected, Solution::is_valid(input.into()));
        }
    }
}
```
