#### rust数据结构与算法

git add --all; git commit -m "normal update"; git push -u origin main

 - 这是结合rust的std库函数和leetcode的题库做(抄)出的答案集，大多都有rust的答案。

 - 除了常见的C++、Python或Java答案方便参考以外，通常会有rust的函数式写法(常常是链式和闭包形式)，更符合人体工程学，方便更改、注释和理解。



##### wrangler 命令

支持 Wrangler 将脚本部署到 Cloudflare，您需要登录到 Cloudflare 帐户完成身份验证。
wrangler login

从此 Worker 初始化新项目
wrangler init --from-dash worker-solitary-credit-db82


将您的项目部署到 Cloudflare 的全球网络：
wrangler publish

Wrangler docs 打开wrangler命令指南


#### 尚未记载：
归并排序 13 14 15
双向链表 14 15 16 17








ai：
在 Hugo 中，你可以通过以下几种方式来自动生成带有特定 Front Matter 的文件：

## 方法1：使用 `--kind` 参数（推荐）

首先创建一个自定义的 archetype 文件：

```bash
# 创建自定义 archetype
cat > archetypes/leetcode.md << 'EOF'
---
title: "{{ .Name }}"
date: {{ .Date }}
tags: ["leetcode", "脑筋急转弯"]
draft: false
---
EOF
```

然后使用该 archetype 生成文件：

```bash
for i in {31..40}; do hugo new --kind leetcode posts/rust-note-$i.md; done
```

## 方法2：使用 `hugo new` 的 `--content` 参数

直接通过命令行传递 front matter：

```bash
for i in {31..40}; do
  hugo new posts/rust-note-$i.md \
    --content "---
title: \"rust-note-$i\"
date: $(date -Iseconds)
tags: [\"leetcode\", \"脑筋急转弯\"]
draft: false
---"
done
```

## 方法3：修改默认 archetype

修改默认的 `archetypes/default.md`：

```bash
# 编辑默认 archetype
cat > archetypes/default.md << 'EOF'
---
title: "{{ .Name }}"
date: {{ .Date }}
tags: ["leetcode", "脑筋急转弯"]
draft: false
---
EOF

# 然后直接生成
for i in {31..40}; do hugo new posts/rust-note-$i.md; done
```

## 方法4：使用 sed 后处理（最灵活）

先创建基础文件，然后用 sed 修改 front matter：

```bash
for i in {31..40}; do
  # 创建文件
  hugo new posts/rust-note-$i.md

  # 修改 front matter
  sed -i '' "s/tags: \[.*\]/tags: [\"leetcode\", \"脑筋急转弯\"]/" posts/rust-note-$i.md
done
```

注意：在 Linux 系统上，`sed -i` 不需要引号，直接 `sed -i "s/.../.../"`；在 macOS 上需要 `sed -i '' "s/.../.../"`

## 方法5：使用 echo 直接创建（不通过 hugo new）

如果不需要 Hugo 自动生成的其他功能：

```bash
for i in {31..40}; do
  cat > content/posts/rust-note-$i.md << EOF
---
title: "rust-note-$i"
date: $(date -Iseconds)
tags: ["leetcode", "脑筋急转弯"]
draft: false
---

## Content

Your content here...
EOF
done
```

## 方法6：使用脚本来实现更复杂的逻辑

创建脚本 `generate_posts.sh`：

```bash
#!/bin/bash

for i in {31..40}; do
  filename="content/posts/rust-note-$i.md"

  cat > "$filename" << EOF
---
title: "rust-note-$i"
date: $(date -Iseconds)
tags: ["leetcode", "脑筋急转弯"]
draft: false
---

## LeetCode Problem

Problem description here...

## Solution

\`\`\`rust
impl Solution {
    pub fn solve() {
        // Your solution here
    }
}
\`\`\`
EOF

  echo "Created: $filename"
done
```

然后运行：
```bash
chmod +x generate_posts.sh
./generate_posts.sh
```

## 推荐方案

**方法1（使用自定义 archetype）** 是最佳实践，因为：
- 保持 Hugo 的标准工作流程
- 可以复用模板
- 便于日后修改
- 支持复杂的 front matter 结构

如果你需要批量修改已存在的文件，可以使用方法4的 sed 方法。
