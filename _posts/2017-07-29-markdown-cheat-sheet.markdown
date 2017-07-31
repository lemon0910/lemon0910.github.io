---
layout:     post
title:      Markdown CheatSheet
date:       2017-07-29 15:19:00
author:     "lemon"
categories: 其它
---

这边文章主要列举常用的markdown语法，使用这些语法足够满足平时90%的写作需求，需要详细需求请参考[详细指南](https://daringfireball.net/projects/markdown/syntax)。需要注意一点，markdown兼容html语法，可以直接在markdown中添加html语句。

<table>
  <tr>
    <td>1</td>
    <td>2</td>
  </tr>
  <tr>
    <td>3</td>
    <td>4</td>
  </tr>
</table>

## 1 标题定义

标题主要有两种类型，setext风格和atx风格，setext风格输入：

```
This is an H1
=============

This is an H2
-------------
```

输出：

This is an H1
=============

This is an H2
-------------

atx风格输入：
```
# Header 1 #
## Header 2 ##
### Header 3 ###    (Hashes on right are optional)
#### Header 4 ####
##### Header 5 #####
###### Header 6 ######
```

输出：

# Header 1 #
## Header 2 ##
### Header 3 ###             (Hashes on right are optional)
#### Header 4 ####
##### Header 5 #####
###### Header 6 ######

## 2 引用

输入：

```
> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.
```

输出：

> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.

## 3 列表

输入：

```
Unordered list 1

*   Red
*   Green
*   Blue

Unordered list 2

+   Red
+   Green
+   Blue

Unordered list 3

-   Red
-   Green
-   Blue

Ordered list 1

1.  Bird
2.  McHale
3.  Parish

Ordered list 2

1.  Bird
1.  McHale
1.  Parish

Ordered list 3

3.  Bird
2.  McHale
1.  Parish
```

输出：

Unordered list 1

*   Red
*   Green
*   Blue

Unordered list 2

+   Red
+   Green
+   Blue

Unordered list 3

-   Red
-   Green
-   Blue

Ordered list 1

1.  Bird
2.  McHale
3.  Parish

Ordered list 2

1.  Bird
1.  McHale
1.  Parish

Ordered list 3

3.  Bird
2.  McHale
1.  Parish

## 4 强调

输入：

```
*single asterisks*

_single underscores_

**double asterisks**

__double underscores__
```

输出：

*single asterisks*

_single underscores_

**double asterisks**

__double underscores__

## 5 链接

输入：

```
This is [an example](http://example.com/ "Title") inline link.

[This link](http://example.net/) has no title attribute.
```

输出：

This is [an example](http://example.com/ "Title") inline link.

[This link](http://example.net/) has no title attribute.

自动链接，输入：

```
<http://markable.in>

<hi@markable.in>
```

输出：

<http://markable.in>

<hi@markable.in>

## 6 图片

输入：

```
![Alt text](/images/github.png "Optional title")

![Baidu Logo](https://ss0.bdstatic.com/5aV1bjqh_Q23odCf/static/superman/img/logo/bd_logo1_31bdc765.png)

![Alt text][id]
[id]: url/to/image  "Optional title attribute"

![Baidu Logo][baidulogo]
[baidulogo]: https://ss0.bdstatic.com/5aV1bjqh_Q23odCf/static/superman/img/logo/bd_logo1_31bdc765.png
```

输出：

![Alt text](/images/github.png)

![Baidu Logo](https://ss0.bdstatic.com/5aV1bjqh_Q23odCf/static/superman/img/logo/bd_logo1_31bdc765.png)

注意一点的是markdown不支持指定图片大小，想要调整图片大小，主要有两种方法：

- 提前处理好图片
- 使用html中的<img />标签

## 7 标尺线

输入：

```
* * *
***
*****
- - -
---------------------------------------
```

输出：

* * *
***
*****
- - -
---------------------------------------

## 8 脚注

输入：

```
Footnotes[^1] have a label[^label] and a definition[^DEF].

[^1]: This is a footnote
[^label]: A footnote on "label"
[^DEF]: The definition of a footnote.
```

输出：

Footnotes[^1] have a label[^label] and a definition[^DEF].

[^1]: This is a footnote
[^label]: A footnote on "label"
[^DEF]: The definition of a footnote.

## 9 表格

输入：

```
| Left align | Right align | Center align |
|:-----------|------------:|:------------:|
| This       |        This |     This     |
| column     |      column |    column    |
| will       |        will |     will     |
| be         |          be |      be      |
| left       |       right |    center    |
| aligned    |     aligned |   aligned    |

* 冒号用来对齐
```

输出：

| Left align | Right align | Center align |
|:-----------|------------:|:------------:|
| This       |        This |     This     |
| column     |      column |    column    |
| will       |        will |     will     |
| be         |          be |      be      |
| left       |       right |    center    |
| aligned    |     aligned |   aligned    |

对于冒号对齐，规则如下：

- `:---` 代表左对齐
- `:--:` 代表居中对齐
- `---:` 代表右对齐

## 10 代码

单个代码，输入：

```
`print` is a python method.
```

输出：

`print` is a python method.

多行代码，使用```包裹代码块，不需要手动缩进。
输入：

    ```
    This is a code block.
    No need to indent manually.
    ```

输出：

```
This is a code block.
No need to indent manually.
```

在第一行```后面添加语言类型，可以展示语法高亮。
输入：

    ```C
    int main() {
        return 0;
    }
    ```

输出：

```C++
int main() {
    return 0;
}
```

## 11 任务表

输入：

```
- [ ] Task 1
- [x] Task 2
- [x] Task 3
```

输出：

- [ ] Task 1
- [x] Task 2
- [x] Task 3

## 12 删除线

输入：

```
这就是 ~~删除线~~
```

输出：

这就是 ~~删除线~~
