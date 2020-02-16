title: 'data-xxx ,attribute'
author: John Doe
date: 2019-02-13 20:27:02
tags:
---
data-* 属性是 HTML 5 的新特性，允许用户在 DOM 中存储自定义信息。

以前，需要存储含有特定含义的信息通常是通过 class 完成的，但这并不是 class 本来的用途。现在，利用 HTML 5，可以为元素添加data-*属性，从而存储自定义信息。其中*是可以自定义的部分。例如：

<article id="tu" data-category="Web Development" data-author="1"> ... </article>
通过 JavaScript 访问
通过 JavaScript 访问自定义的信息有两种方式：getAttribute()和dataset。

getAttribute 方法
这就是经典的取得一个元素属性的方式，和以前一样。

document.getElementById('tu').getAttribute('data-category'); // "Web Development"
dataset 方法
这是 HTML 5 新增的方法，可以更方便的读取所有的 data 信息。

var article = document.getElementById('tu');
var data = article.dataset;
alert(data.category); // "Web Development"
alert(data.author); // 1
通过 jQuery 访问
jQuery 也提供了专门的data方法来访问这些信息：

$("#tu").data('category'); // "Web Development"
通过 CSS 访问
使用 attr
article::before {
    content: attr(data-category);
}
使用属性选择器
article[data-author='1'] {
    border-width: 1px;
}
修改信息
在引入 jQuery 之前，使用 JavaScript 修改自定义信息也非常容易，两种方式都可以：

var a = document.getElementById('tu');
a.dataset.category = "Uncategorized";
a.setAttribute("data-category", "Uncategorized");
然而如果你用 jQuery 的data方法进行修改：

$("#tu").data("category", "Uncategorized");
会发现 HTML 代码并没有改动，同时使用dataset等标准 JavaScript 读出来的数据也是修改之前的数据。类似，如果先用 jQuery 读取一次，然后再用标准 JavaScript 修改，再用 jQuery 读取第二次，则发现读取的数据仍是修改之前的数据。可是如果没有经过 jQuery 读取直接用标准 JavaScript 修改，这时再用 jQuery 读取，读到的就是新数据了。

这是怎么回事儿？

jQuery 的文档对此有所说明：

The data- attributes are pulled in the first time the data property is accessed and then are no longer accessed or mutated (all data values are then stored internally in jQuery).

原来，标准 JavaScript 直接对 DOM 进行读写（和我们设想的一致），但是 jQuery 不同。jQuery 的内部也维护着一份这些 data 数据。当第一次使用 jQuery 读取时，数据被从 DOM 读到jQuery.cache保存起来，以后使用 jQuery 修改时，修改的是内部维护的这个数据，同时再次读取也会从 jQuery 内部而非 DOM 读取数据。

因此，最好的办法是，要么一直用标准 JavaScript，要么一直用 jQuery。不要两者混用就好了。jQuery 如此设计的初衷就是为了减少 DOM 读写从而提高性能。
