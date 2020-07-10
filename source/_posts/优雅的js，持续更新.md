---
title: 优雅的js，持续更新
date: 2020-07-10 14:09:50
tags:
categories: 
- js
---
# 解构赋值
## 删除对象不需要的属性的值
```js
const obj = {
    name: 'detanx',
    age: 24,
    height: 180
}
// 删除height
const { height, ...otherObj } = obj;
// otherObj => {name: 'detanx', age: 24}
```
---
## 交换两个变量的值
```js
let x = 1, y = 2;
[x, y] = [y, x]
// x: 2,y: 1
```

---
## 在解构中使用别名
```js
const object = { number: 10 }
const { number } = object
// 使用别名
const { number: otherNumber } = object
console.log(otherNumber) //10
```
---
# 字符串
## 判断目标字符是否包含某个字符串
```js
let str1 = 'abcdefg';
let str2 = 'bcd';

// 一般写法
if (str1.indexOf(str2) !== -1) {
  return true;
} else {
  return false;
}
// 优雅写法
return str1.includes(str2); // str1.includes(str2,2) //从角标为2的位置开始判断
 
```
---
## 字符串补全
```js
// 从左边开始补全，时间格式补全
'5'.padStart(2, '0'); // '05'
'04'.padStart(10, 'YYYY-MM-DD'); // 'YYYY-MM-04'
//从右边补全
'abc'.padEnd(10, "foo");   // "abcfoofoof"
```
---

# 数组操作
## 复杂数组筛选
```js
let arr1 = ['1', '2', '3', '4'];
let arr2 = [{id: '1', name: 'a'}, {id: '5', name: 'b'}, {id: '6', name: 'c'}];
let result;
// 一般写法
arr1.forEach(item => {
    arr2.forEach((obj) => {
        if (item === obj.id) {
            result = obj;
            return;
        }
    })
});
// 优雅写法
arr1.some((item) => {
    return result = arr2.filter(obj => obj.id === item);
})
console.log(result); // {id: '1', name: 'a'}
```
---
