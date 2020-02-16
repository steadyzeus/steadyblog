title: JavaScript 二进制 十进制...
author: John Doe
date: 2019-07-19 06:32:10
tags:
---
利用 toString() 方法 十进制转二进制

var num = 10；
console.log( num.toString("2") )  转2进制: "1010"

console.log( num.toString("8") )  转8进制: "12"

console.log( num.toString("16") )  转16进制: "a"

利用 parseInt() 方法，其它进制转十进制

var num = 100

var num2 = num.toString("2")  //  "1100100"

var num8 = num.toString("8")  // "144"
var num16 = num.toString("16")  // "64"

console.log( parseInt(num2,2) )  2转10进制输出: 100

console.log( parseInt(num8 , 8) )  8转10进制: 100
console.log( parseInt(num16 , 16) )  16转10进制: 100
