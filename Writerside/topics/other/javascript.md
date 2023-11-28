# Javascript

## var

+ 函数作用域：function scope
+ 块作用域：block scope
+ 全局作用域：global scope
+ var声明的变量没有块作用域，变量声明会被提到最开始，但不会被赋值;
+ let/const 有块作用域
```javascript
if (false) {
  var name = 'Matt';
  console.log(name);
}
console.log(name);
```
+ [参考链接](https://zh.javascript.info/var)
+ 