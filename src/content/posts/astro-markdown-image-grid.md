---
title: 在 Astro 博客中为 Markdown 添加多图并排的自适应网格画廊功能
description: 通过编写自定义的 Remark 插件，实现 Markdown 文章中非常实用的多图并排自适应画廊效果。
published: 2026-03-27
tags: [Astro, Markdown, Remark, 运维]
category: 开发笔记
lang: zh-CN
image: /images/scthd4-0.webp
---

## 背景

在编写技术博客或是日常分享记录时，我们经常会遇到**需要将几张照片（如对比图、风景照片）并排展示**的需求。

默认的 Markdown 语法下，直接把好几张图写在一起只会根据屏幕宽度往下折行排布，并不能得到整齐划一的画廊（Gallery）排版效果。如果要使用原生的 HTML 写栅格代码，又会严重破坏 Markdown 中原始书写的流畅感与整洁度。

为了解决这个问题，我决定借助 [Unified 体系](https://unifiedjs.com/) 中的 **Remark 插件**，在 Astro 环境里无缝开发一个自定义标签 `[grid]`。只要将图片放进这个标签里，系统就会自动将它们变为自适应的响应式网格布局并自动补齐对齐高度！

这个功能使用 `Claude Code` 协助完成。

## 插件最终效果与语法

设想中的语法十分简单，只要用 `[grid]` 和 `[/grid]` 包裹住常规的 Markdown 图片语法即可。

例如两张照片的代码如下：

```
[grid]

![示例1](image_url_1)

![示例2](image_url_2)

![示例3](image_url_1)

[/grid]
```

**最终效果**

![image-grid](/images/scthd4-0.webp)

由于我们在底层介入了 AST（抽象语法树），渲染出的结果将不再是松散的图片，而是带有 Tailwind 栅格属性（`grid`, `grid-cols-2` 等）的容器，并且**它可以依据内部包了两图还是三图做到自动计算列数进行展现**。

## 原理分析：AST 语法树与 Remark

在我们的博客打包全流程（Markdown -> HTML）中，主要经历下面两个核心生态的转换：

1. **Remark (mdast)**: 解析 Markdown，生成抽象语法树 (AST)。
2. **Rehype (hast)**: 接手 AST 将其转换为 HTML 并添加结构和类名。

我们要实现在 **Remark 阶段**截获特定的明文标记 `[grid]` 并进行打包。核心在于利用 `unist-util-visit` 库遍历这段抽象树中的所有 `paragraph`（段落），如果我们在段落头部发现了 `[grid]`，我们就可以通过将其转换成特定的 HTML `div` 节点来渲染出网格。

## 第一步：编写核心插件 `remark-image-grid.js`

我们在 `src/plugins/remark-image-grid.js` 下编写自己的模块处理器：

```javascript
import { visit } from "unist-util-visit";

/**
 * Custom Remark plugin for creating responsive image grids.
 *
 * It parses markdown blocks surrounded by `[grid]` and `[/grid]` tags and wraps
 * the contained images in a styled `div` container with a grid layout.
 * The column count is evaluated automatically based on the number of inserted images
 * inside the grid tags (up to 4 columns).
 */
export function remarkImageGrid() {
  return (tree) => {
    // 聚焦根节点，以便收集我们要进行重组的元素
    if (tree.type === 'root') {
      const newChildren = [];
      let inGrid = false;
      let gridChildren = [];

      for (let i = 0; i < tree.children.length; i++) {
        let node = tree.children[i];

        // 识别包含图片的段落中的 [grid] 或者 [/grid] 标记
        if (node.type === 'paragraph' && node.children.length > 0) {
          const first = node.children[0];
          const last = node.children[node.children.length - 1];

          let containsGridStart = false;
          let containsGridEnd = false;

          if (first.type === 'text' && first.value.trim().startsWith('[grid]')) {
            containsGridStart = true;
          }
          if (last.type === 'text' && last.value.trim().endsWith('[/grid]')) {
             containsGridEnd = true;
          }

          // Case: 当 [grid] 标签跨越不同段落（中间有空行换行）时
          if (!inGrid && containsGridStart) {
            inGrid = true;
            // 清理语法标签文本本身
            first.value = first.value.replace(/^\s*\[grid\]\s*/, '');
            if (node.children.length === 1 && first.value.trim() === '') {
               // 处理它自成一行的极端情况
            } else {
               gridChildren.push(node);
            }
            continue;
          }

          if (inGrid && containsGridEnd) {
             inGrid = false;
             last.value = last.value.replace(/\s*\[\/grid\]\s*$/, '');
             if (node.children.length === 1 && last.value.trim() === '') {
             } else {
                 gridChildren.push(node);
             }

             // ✨ 重点：动态计数，根据包含的图片自动计算列数，最高支持一行四列
             let imgCount = 0;
             gridChildren.forEach(child => {
                 visit(child, 'image', () => { imgCount++; });
             });
             const cols = imgCount || 2;
             const mdColClass = cols === 1 ? 'md:grid-cols-1' :
                                cols === 2 ? 'md:grid-cols-2' :
                                cols === 3 ? 'md:grid-cols-3' : 'md:grid-cols-4';

             // 将节点重新推入为一个自带 class 定义的 HTML 结构节点！
             newChildren.push({
               type: 'paragraph',
               data: {
                 hName: 'div',
                 hProperties: { className: ['image-grid', 'grid', 'grid-cols-1', mdColClass, 'gap-4', 'my-4'] }
               },
               children: gridChildren
             });
             gridChildren = []; // 初始化留待下次使用
             continue;
          }
        }

        // 把普通的非图节点正常塞回 AST 中
        if (inGrid) {
           gridChildren.push(node);
        } else {
           newChildren.push(node);
        }
      }

      // 防止用户忘记写闭合标签，帮他们补全推入
      if (inGrid) {
        newChildren.push(...gridChildren);
      }

      tree.children = newChildren;
    }
  };
}
```

以上的逻辑非常巧妙地做到了几点：

- 处理了用户**是否在标签里换行**的问题（AST 会将其切割为不同的 paragraph 段落，所以需要用类似流的状态机 `inGrid` 变量开启拦截器）；
- **动态栅格分配**：利用 Tailwind 中 `grid` 属性灵活分配每一行的占比；

## 第二步：在 Astro 中引入该插件

只需前往项目的 `astro.config.mjs` 中登记上这一插件，它就会自动在我们构建（build）时注入生效了！

```javascript
import { remarkImageGrid } from "./src/plugins/remark-image-grid.js";

// ...略去无关代码...

export default defineConfig({
  markdown: {
    remarkPlugins: [
      remarkImageGrid, // 我们自定义开发的网格插件
      // ...其他remark插件,
    ],
  }
})
```

## 第三步：CSS 兜底高度对齐防翻车

如果你测试一下可能会发现一个**致命问题**：虽然左右并排了！但是左侧的图片是个正方形，右边是一张扁平的长图，这就导致底下生成的图注（figcaption）一高一低参差不齐！

因此我们需要结合写好的 `image-grid` 父类在公共 CSS 样式中添加一套自适应填充满容器宽高的**裁剪逻辑**：

```css
.image-grid {
  @apply items-stretch;

  > figure {
    @apply flex flex-col h-full w-full m-0 p-0;

    img {
      /* object-cover魔法让它中心裁剪完美等高，杜绝形变扭曲 */
      @apply object-cover w-full flex-grow rounded-xl m-0;
      height: 100%;
    }

    figcaption {
      @apply mt-2 mb-0; /* 将图注控制在同一底部水平线上 */
    }
  }
}
```

这里利用 Tailwind (或者纯 CSS 也可) 使用给图片层添加 `height: 100%` 设置并用 `object-cover` 进行了中心修剪，这不仅使得画面毫无拉扯而且强制它们保证在了一排同样完美的基线当中！

## 结语

通过仅仅一段几十行的轻量 AST Remark 劫持逻辑以及搭配了几行 CSS。我们就十分优雅地给自己的博客注入了超酷的相册画廊（Image Grid）能力。这种完全基于 AST 标准流的方式比通过客户端注入 JS 会产生更少的性能消耗（因为它完全在服务端预渲染 HTML 阶段组装结束了）。
