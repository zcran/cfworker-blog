---
title: "Leetcode 双向链表8"
date: 2025-06-16T22:16:50+08:00
tags: ["leetcode", "双向链表"]
draft: false
---

## 设计一个文本编辑器

请你设计一个带光标的文本编辑器，它可以实现以下功能：

添加：在光标所在处添加文本。
删除：在光标所在处删除文本（模拟键盘的删除键）。
移动：将光标往左或者往右移动。
当删除文本时，只有光标左边的字符会被删除。光标会留在文本内，也就是说任意时候 0 <= cursor.position <= currentText.length 都成立。

请你实现 TextEditor 类：

TextEditor() 用空文本初始化对象。
void addText(string text) 将 text 添加到光标所在位置。添加完后光标在 text 的右边。
int deleteText(int k) 删除光标左边 k 个字符。返回实际删除的字符数目。
string cursorLeft(int k) 将光标向左移动 k 次。返回移动后光标左边 min(10, len) 个字符，其中 len 是光标左边的字符数目。
string cursorRight(int k) 将光标向右移动 k 次。返回移动后光标左边 min(10, len) 个字符，其中 len 是光标左边的字符数目。


示例 1：

输入：
["TextEditor", "addText", "deleteText", "addText", "cursorRight", "cursorLeft", "deleteText", "cursorLeft", "cursorRight"]
[[], ["leetcode"], [4], ["practice"], [3], [8], [10], [2], [6]]
输出：
[null, null, 4, null, "etpractice", "leet", 4, "", "practi"]

解释：
TextEditor textEditor = new TextEditor(); // 当前 text 为 "|" 。（'|' 字符表示光标）
textEditor.addText("leetcode"); // 当前文本为 "leetcode|" 。
textEditor.deleteText(4); // 返回 4
                          // 当前文本为 "leet|" 。
                          // 删除了 4 个字符。
textEditor.addText("practice"); // 当前文本为 "leetpractice|" 。
textEditor.cursorRight(3); // 返回 "etpractice"
                           // 当前文本为 "leetpractice|".
                           // 光标无法移动到文本以外，所以无法移动。
                           // "etpractice" 是光标左边的 10 个字符。
textEditor.cursorLeft(8); // 返回 "leet"
                          // 当前文本为 "leet|practice" 。
                          // "leet" 是光标左边的 min(10, 4) = 4 个字符。
textEditor.deleteText(10); // 返回 4
                           // 当前文本为 "|practice" 。
                           // 只有 4 个字符被删除了。
textEditor.cursorLeft(2); // 返回 ""
                          // 当前文本为 "|practice" 。
                          // 光标无法移动到文本以外，所以无法移动。
                          // "" 是光标左边的 min(10, 0) = 0 个字符。
textEditor.cursorRight(6); // 返回 "practi"
                           // 当前文本为 "practi|ce" 。
                           // "practi" 是光标左边的 min(10, 6) = 6 个字符。


```
pub struct TextEditor {
    left: Vec<u8>,  // 光标左侧字符，按顺序存储
    right: Vec<u8>, // 光标右侧字符，逆序存储
}

impl TextEditor {
    // 创建空的文本编辑器，光标在起始位置。
    fn new() -> Self {
        TextEditor {
            left: Vec::new(),
            right: Vec::new(),
        }
    }

    // 在光标位置插入文本
    // 新文本添加到 left 的末尾（光标左侧）
    // 光标自动移动到插入文本的末尾
    fn add_text(&mut self, text: String) {
        self.left.extend(text.as_bytes());
    }

    // 删除光标左侧的 k 个字符
    // 实际删除数量 = min(k, 左侧字符数)
    // 返回实际删除的字符数
    fn delete_text(&mut self, k: i32) -> i32 {
        let delete_count = k.min(self.left.len() as i32);
        self.left.truncate(self.left.len() - delete_count as usize);
        delete_count
    }

    // 返回光标左侧最多10个字符（题目要求）
    // 如果左侧字符少于10个，返回全部
    // 用于显示当前光标位置前的文本
    fn text(&self) -> String {
        let start = (self.left.len() as i32 - 10).max(0) as usize;
        String::from_utf8(self.left[start..].to_vec()).unwrap()
    }

    // 光标向左移动 k 步
    // 每次移动：从 left 弹出字符，推入 right
    // 返回移动后光标左侧最近10个字符
    fn cursor_left(&mut self, mut k: i32) -> String {
        while k > 0 && !self.left.is_empty() {
            if let Some(byte) = self.left.pop() {
                self.right.push(byte);
            }
            k -= 1;
        }
        self.text()
    }


    // 光标向右移动 k 步
    // 每次移动：从 right 弹出字符，推入 left
    // 返回移动后光标左侧最近10个字符
    fn cursor_right(&mut self,mut k: i32) -> String {
        while k > 0 && !self.right.is_empty() {
            if let Some(byte) = self.right.pop() {
                self.left.push(byte);
            }
            k -= 1;
        }
        self.text()
    }
}

```

左右栈

设计优势

高效光标移动：光标移动是 O(1) 操作（每个字符只移动一次）
就地编辑：插入和删除操作都在向量末尾进行，避免频繁的数据搬移
内存友好：使用 Vec<u8> 而不是 String，避免了 UTF-8 验证开销

关键技巧

逆序存储右侧字符：使得光标移动时只需 pop 和 push，无需遍历
延迟字符串构造：只在需要显示时才构建字符串（text() 方法）
安全的 UTF-8 转换：使用 String::from_utf8().unwrap()（假设输入总是有效 UTF-8）
