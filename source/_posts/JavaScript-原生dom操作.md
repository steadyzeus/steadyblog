title: JavaScript 原生dom操作
author: John Doe
date: 2019-12-11 22:52:35
tags:
---
### document.querySelector / document.querySelectorAll
document.querySelector方法返回文档中与指定选择器或选择器组匹配的第一个 html 元素。 如果找不到匹配项，则返回null。
document.querySelectorAll 方法返回与指定的选择器组匹配的文档中的元素列表 (使用深度优先的先序遍历文档的节点)。返回的对象是 NodeList 。
// 返回第一个 ul 元素
const list = document.querySelector('ul')
// 返回所有类名为 info 或者 warning 的 div 元素
const elements = document.querySelectorAll('div.info, div.warning');


### Node.removeChild
Node.removeChild方法从DOM中删除一个子节点并返回删除的节点。 请注意，返回的节点不再是DOM的一部分，而是仍存在于内存中。 如果处理不当，可能会导致内存泄漏。
let list = document.querySelector('ul');
let firstItem = list.querySelector('li');
let removedItem = list.removeChild(firstItem);

### Node.replaceChild
此方法替换父节点中的子节点(并返回替换后的旧子节点)。请注意，如果处理不当，此方法可能导致与Node.removeChild类似的内存泄漏问题。
let list = document.querySelector('ul');
let oldItem = list.querySelector('li');
let newItem = document.createElement('li');
newItem.innerHTML = '前端小智';
let replacedItem = list.replaceChild(newItem, oldItem);
复

### Element.insertAdjacentHTML
element.insertAdjacentHTML(position, text) 将指定的文本解析为HTML或XML，并将结果节点插入到DOM树中的指定位置。它不会重新解析它正在使用的元素，因此它不会破坏元素内的现有元素。这避免了额外的序列化步骤，使其比直接innerHTML操作更快。
position是相对于元素的位置，并且必须是以下字符串之一：
beforebegin：元素自身的前面。
afterbegin：插入元素内部的第一个子节点之前。
beforeend：插入元素内部的最后一个子节点之后。
afterend：元素自身的后面。
text是要被解析为HTML或XML,并插入到DOM树中的字符串。
<!-- beforebegin -->
<div>
  <!-- afterbegin -->
  <p>Hello World</p>
  <!-- beforeend -->
</div>
<!-- afterend -->
复制代码
示例：
var list = document.querySelector('ul');
list.insertAdjacentHTML('afterbegin', '<li id="first-item">First</li>');


### Element.hasAttribute / Element.removeAttribute
Element.hasAttribute方法检查给定元素是否具有指定的属性，返回值为boolean。 通过调用Element.removeAttribute方法，我们可以从元素中删除具有给定名称的属性。
let list = document.querySelector('ul');
if (list.hasAttribute('id')) {
    console.log('list has an id');
    list.removeAttribute('id');
};


### Element.getAttribute / Element.setAttribute
Element.getAttribute方法返回元素上给定属性的值，反之亦然，Element.setAttribute设置给定元素上属性的值。
let list = document.querySelector('ul');
list.setAttribute('id', 'my-list');
let id = list.getAttribute('id');
console.log(id); // outputs my-list



