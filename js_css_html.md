---
title: js_css_html 
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---
# 01 杂项
 1. css优先级： \* < 标签 < class < ID < 行间
 2. js修改style只能修改行间css，读取style也是读取行间，因此如果js操作style以后再操作className则不起效果。
 3. js操作dom元素时方括号和用.操作属性是等价的。但是方括号[]可以传入变量['className']
 4. 匿名函数，直接将函数定义去掉函数名后放进去就行了。
 5. window.onload() = function() {...}在页面加载完毕时执行,若将js直接放在```<head></head>```中间，则可能会出错，应为此时body还没有加载。
 6. this 当前操作的对象