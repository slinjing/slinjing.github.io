---
title: Chirpy 文本和排版
date: 2023-08-08 11:33:00 +0800
categories: [Blogging]
tags: [Chirpy]
---

## 标题
```md
<!-- markdownlint-capture -->
<!-- markdownlint-disable -->
# H1 — heading
{: .mt-4 .mb-0 }

## H2 — heading
{: data-toc-skip='' .mt-4 .mb-0 }

### H3 — heading
{: data-toc-skip='' .mt-4 .mb-0 }

#### H4 — heading
{: data-toc-skip='' .mt-4 }
<!-- markdownlint-restore -->
```

<!-- markdownlint-capture -->
<!-- markdownlint-disable -->
# H1 — heading
{: .mt-4 .mb-0 }

## H2 — heading
{: data-toc-skip='' .mt-4 .mb-0 }

### H3 — heading
{: data-toc-skip='' .mt-4 .mb-0 }

#### H4 — heading
{: data-toc-skip='' .mt-4 }
<!-- markdownlint-restore -->

## 段落

Quisque egestas convallis ipsum, ut sollicitudin risus tincidunt a. Maecenas interdum malesuada egestas. Duis consectetur porta risus, sit amet vulputate urna facilisis ac. Phasellus semper dui non purus ultrices sodales. Aliquam ante lorem, ornare a feugiat ac, finibus nec mauris. Vivamus ut tristique nisi. Sed vel leo vulputate, efficitur risus non, posuere mi. Nullam tincidunt bibendum rutrum. Proin commodo ornare sapien. Vivamus interdum diam sed sapien blandit, sit amet aliquam risus mattis. Nullam arcu turpis, mollis quis laoreet at, placerat id nibh. Suspendisse venenatis eros eros.

## 列表

### 有序列表
```md
1. Firstly
2. Secondly
3. Thirdly
```

1. Firstly
2. Secondly
3. Thirdly

### 无序列表
```md
- Chapter
  - Section
    - Paragraph
```

- Chapter
  - Section
    - Paragraph

### 待办事项清单
```md
- [ ] Job
  - [x] Step 1
  - [x] Step 2
  - [ ] Step 3
```

- [ ] Job
  - [x] Step 1
  - [x] Step 2
  - [ ] Step 3

### 描述列表
```md
太阳
: 地球围绕其旋转的恒星

月亮
: 地球的天然卫星，通过反射太阳光可见
```

太阳
: 地球围绕其旋转的恒星

月亮
: 地球的天然卫星，通过反射太阳光可见

## 区块引用
```md
> 此行显示块引用。
```

> 此行显示块引用。

## 提示
```md
<!-- markdownlint-capture -->
<!-- markdownlint-disable -->
> An example showing the `tip` type prompt.
{: .prompt-tip }

> An example showing the `info` type prompt.
{: .prompt-info }

> An example showing the `warning` type prompt.
{: .prompt-warning }

> An example showing the `danger` type prompt.
{: .prompt-danger }
<!-- markdownlint-restore -->
```


<!-- markdownlint-capture -->
<!-- markdownlint-disable -->
> An example showing the `tip` type prompt.
{: .prompt-tip }

> An example showing the `info` type prompt.
{: .prompt-info }

> An example showing the `warning` type prompt.
{: .prompt-warning }

> An example showing the `danger` type prompt.
{: .prompt-danger }
<!-- markdownlint-restore -->

## 表格
```md
| Company                      | Contact          | Country |
| :--------------------------- | :--------------- | ------: |
| Alfreds Futterkiste          | Maria Anders     | Germany |
| Island Trading               | Helen Bennett    |      UK |
| Magazzini Alimentari Riuniti | Giovanni Rovelli |   Italy |
```

| Company                      | Contact          | Country |
| :--------------------------- | :--------------- | ------: |
| Alfreds Futterkiste          | Maria Anders     | Germany |
| Island Trading               | Helen Bennett    |      UK |
| Magazzini Alimentari Riuniti | Giovanni Rovelli |   Italy |

## 链接
```md
<http://127.0.0.1:4000>
```

<http://127.0.0.1:4000>

## 脚注
```md
单击钩子将定位到脚注[^footnote], 这里是另一个脚注[^fn-nth-2]。
```

单击钩子将定位到脚注[^footnote], 这里是另一个脚注[^fn-nth-2]。

## 内联代码
```md
这是的一个例子 `Inline Code`。
```

这是的一个例子 `Inline Code`。

## 文件路径
```md
这是 `/path/to/the/file.extend`{: .filepath}。
```

这是 `/path/to/the/file.extend`{: .filepath}。

## 代码块

### 常见的

```text
This is a common code snippet, without syntax highlight and line number.
```

### 具体语言

```bash
if [ $? -ne 0 ]; then
  echo "The command was not successful.";
  #do the needful / exit
fi;
```

### 具体文件名

```sass
@import
  "colors/light-typography",
  "colors/dark-typography";
```
{: file='_sass/jekyll-theme-chirpy.scss'}


## 图片

### 默认（带标题）
```md
![Desktop View](/img/avatar.png){: width="972" height="589" }
_全屏宽度和居中对齐_
```

![Desktop View](/img/avatar.png){: width="972" height="589" }
_全屏宽度和居中对齐_

### 左对齐
```md
![Desktop View](/img/avatar.png){: width="972" height="589" .w-75 .normal}
```

![Desktop View](/img/avatar.png){: width="972" height="589" .w-75 .normal}

### 向左浮动
```md
![Desktop View](/img/avatar.png){: width="972" height="589" .w-50 .left}
Praesent maximus aliquam sapien. Sed vel neque in dolor pulvinar auctor. Maecenas pharetra, sem sit amet interdum posuere, tellus lacus eleifend magna, ac lobortis felis ipsum id sapien. Proin ornare rutrum metus, ac convallis diam volutpat sit amet. Phasellus volutpat, elit sit amet tincidunt mollis, felis mi scelerisque mauris, ut facilisis leo magna accumsan sapien. In rutrum vehicula nisl eget tempor. Nullam maximus ullamcorper libero non maximus. Integer ultricies velit id convallis varius. Praesent eu nisl eu urna finibus ultrices id nec ex. Mauris ac mattis quam. Fusce aliquam est nec sapien bibendum, vitae malesuada ligula condimentum.
```

![Desktop View](/img/avatar.png){: width="972" height="589" .w-50 .left}
Praesent maximus aliquam sapien. Sed vel neque in dolor pulvinar auctor. Maecenas pharetra, sem sit amet interdum posuere, tellus lacus eleifend magna, ac lobortis felis ipsum id sapien. Proin ornare rutrum metus, ac convallis diam volutpat sit amet. Phasellus volutpat, elit sit amet tincidunt mollis, felis mi scelerisque mauris, ut facilisis leo magna accumsan sapien. In rutrum vehicula nisl eget tempor. Nullam maximus ullamcorper libero non maximus. Integer ultricies velit id convallis varius. Praesent eu nisl eu urna finibus ultrices id nec ex. Mauris ac mattis quam. Fusce aliquam est nec sapien bibendum, vitae malesuada ligula condimentum.



### 向右浮动



```md
![Desktop View](/img/avatar.png){: width="972" height="589" .w-50 .right}
Praesent maximus aliquam sapien. Sed vel neque in dolor pulvinar auctor. Maecenas pharetra, sem sit amet interdum posuere, tellus lacus eleifend magna, ac lobortis felis ipsum id sapien. Proin ornare rutrum metus, ac convallis diam volutpat sit amet. Phasellus volutpat, elit sit amet tincidunt mollis, felis mi scelerisque mauris, ut facilisis leo magna accumsan sapien. In rutrum vehicula nisl eget tempor. Nullam maximus ullamcorper libero non maximus. Integer ultricies velit id convallis varius. Praesent eu nisl eu urna finibus ultrices id nec ex. Mauris ac mattis quam. Fusce aliquam est nec sapien bibendum, vitae malesuada ligula condimentum.
```

![Desktop View](/img/avatar.png){: width="972" height="589" .w-50 .right}
Praesent maximus aliquam sapien. Sed vel neque in dolor pulvinar auctor. Maecenas pharetra, sem sit amet interdum posuere, tellus lacus eleifend magna, ac lobortis felis ipsum id sapien. Proin ornare rutrum metus, ac convallis diam volutpat sit amet. Phasellus volutpat, elit sit amet tincidunt mollis, felis mi scelerisque mauris, ut facilisis leo magna accumsan sapien. In rutrum vehicula nisl eget tempor. Nullam maximus ullamcorper libero non maximus. Integer ultricies velit id convallis varius. Praesent eu nisl eu urna finibus ultrices id nec ex. Mauris ac mattis quam. Fusce aliquam est nec sapien bibendum, vitae malesuada ligula condimentum.

### 暗/亮模式和阴影

下面的图片将根据主题偏好切换暗/亮模式，注意它有阴影。

```md
![light mode only](/img/avatar.png){: .light .w-75 .shadow .rounded-10 w='1212' h='668' }
![dark mode only](/img/avatar.png){: .dark .w-75 .shadow .rounded-10 w='1212' h='668' }
```


![light mode only](/img/avatar.png){: .light .w-75 .shadow .rounded-10 w='1212' h='668' }
![dark mode only](/img/avatar.png){: .dark .w-75 .shadow .rounded-10 w='1212' h='668' }

## 视频
```md
{% include embed/youtube.html id='Balreaj8Yqs' %}
```

{% include embed/youtube.html id='Balreaj8Yqs' %}

## 反向脚注
```md
[^footnote]: 脚注来源
[^fn-nth-2]: 第二个脚注来源
```

[^footnote]: 脚注来源
[^fn-nth-2]: 第二个脚注来源

