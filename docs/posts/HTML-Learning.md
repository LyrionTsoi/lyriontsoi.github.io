---
title: HTML Learning
date: 2023-01-16 21:13:26
tags:
- hmlt
categories:
- 前端
- HTML
---

# html 基础标签

- [html资料查询（MDN）](https://developer.mozilla.org/zh-CN/)

## 文件结构

**文档结构**
html的所有标签为树形结构，例如：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Web应用课</title>
</head>
<body>
    <h1>第一讲</h1>
</body>
</html>
```

在vscode刚创建一个新的html文件，打出来第一个`!`感叹号接着按下`Tap`键就可以快速创建上面的模版。

<b>`<html>`标签</b>

HTML <html> 元素 表示一个 HTML 文档的根（顶级元素），所以它也被称为根元素。所有其他元素必须是此元素的后代。

**`<head>标签`**

HTML head 元素 规定文档相关的配置信息（元数据），包括文档的标题，引用的文档样式和脚本等。

**`<body>标签`**

HTML body 元素表示文档的内容。document.body 属性提供了可以轻松访问文档的 body 元素的脚本。

**`<meta>`**

HTML <meta> 元素表示那些不能由其它 HTML 元相关（meta-related）元素（(<base>、<link>, <script>、<style> 或 <title>）之一表示的任何元数据信息。

常见属性：

charset：这个属性声明了文档的字符编码。如果使用了这个属性，其值必须是与 ASCII 大小写无关（ASCII case-insensitive）的”utf-8”。
name：name 和 content 属性可以一起使用，以名 - 值对的方式给文档提供元数据，其中 name 作为元数据的名称，content 作为元数据的值。

**`<title>`**

HTML <title> 元素 定义文档的标题，显示在浏览器的标题栏或标签页上。它只应该包含文本，若是包含有标签，则它包含的任何标签都将被忽略。

**`<icon>`**

icon：图标

```html
<link rel="icon" href="images/icon.png">
```

## 文本标签

文本标签虽然很多，但大部分可看成是预定好样式的<div>和<span>。所以重点在<div> 、<span>.

**`<div>`**

`<div>`元素 (或 HTML 文档分区元素) 是一个通用型的流内容容器，在不使用CSS的情况下，其对内容或布局没有任何影响。
其他块级标签例如：<h1>, <p>, <pre>, <ul>, <ol>, <table>。

**`<span>`**

`<span>`元素是短语内容的通用行内容器，并没有任何特殊语义。可以使用它来编组元素以达到某种样式意图（通过使用类或者 Id 属性），或者这些元素有着共同的属性，比如lang。应该在没有其他合适的语义元素时才使用它。**<span> 与 <div> 元素很相似，但 <div> 是一个块元素而 <span> 则是行内元素**
其他内联标签例如：<i>, <b>, <del>, <ins>, <td>, <a>

**`<h1> - <h6>`**

HTML <h1>–<h6> 标题 (Heading) 元素呈现了六个不同的级别的标题，<h1> 级别最高，而 <h6> 级别最低。

**`<p>`**

**段落**

HTML <p>元素（或者说 HTML 段落元素）表示文本的一个段落。该元素通常表现为一整块与相邻文本分离的文本，或以垂直的空白隔离或以首行缩进。另外，<p> 是块级元素。

**`<pre>`**

**等宽字体**

HTML <pre> 元素表示预定义格式文本。在该元素中的文本通常按照原文件中的编排，以**等宽字体**的形式展现出来，文本中的空白符（比如空格和换行符）都会显示出来。(紧跟在 <pre> 开始标签后的换行符也会被省略)

**`<br>`**

**换行**

HTML `<br>`元素在文本中生成一个换行（回车）符号。此元素在写诗和地址时很有用，这些地方的换行都非常重要。

**`<hr>`**

**没什么用**

HTML `<hr>` 元素表示段落级元素之间的主题转换（例如，一个故事中的场景的改变，或一个章节的主题的改变）。在 HTML 的早期版本中，它是一个水平线。现在它仍能在可视化浏览器中表现为水平线，但目前被定义为语义上的，而不是表现层面上。所以如果想画一条横线，请使用适当的 css 样式来修饰。

**`<i>`**

**斜体**

HTML 元素 <i> 用于表现因某些原因需要区分普通文本的一系列文本。例如技术术语、外文短语或是小说中人物的思想活动等，它的内容通常以斜体显示。

**`<b>`**

**粗体**

HTML 提醒注意（Bring Attention To）元素（<b>）用于吸引读者的注意到该元素的内容上（如果没有另加特别强调）。这个元素过去被认为是粗体（Boldface）元素，并且大多数浏览器仍然将文字显示为粗体。尽管如此，你不应将 <b> 元素用于显示粗体文字；替代方案是使用 CSS font-weight 属性来创建粗体文字。

**`<del>`**

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202301162113483.png)

**`<ins>`**

**下划线**

HTML <ins> 元素定义已经被插入文档中的文本。

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202301162113545.png)
