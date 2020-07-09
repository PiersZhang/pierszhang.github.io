---
title: Vue2.X和MobX中的Object.defineProperty，并与Proxy的对比
date: 2020-07-09 17:47:48
tags:
---
如题，Vue是一个js框架，MobX则是React进行状态管理的一个库，两者看起来并没有什么关联，但是由于都使用了Object.defineProperty，使得两者在数据绑定操作上不禁有些相似

由于Vue3.X已经用Proxy重写了他的数据绑定机制，所以顺带了解一下Proxy
###本文介绍了什么
+ Object.defineProperty与Proxy的使用
+ Object.defineProperty与Proxy的缺点 
+ Object.defineProperty在Vue和MobX中造成的问题和解决办法
### Object.defineProperty与Proxy的使用
##### Object.defineProperty [Object.defineProperty - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)
```
/*
* obj : 要定义属性的对象
* prop : 要定义或修改的属性的名称或 [`Symbol`]
* descriptor : 要定义或修改的属性描述符
*    value： 该属性对应的值
*    writable
*    enumerable
*    configurable
*    get
*    set
*/
var o = {};
Object.defineProperty(o, "a", {
  value : 37,
  writable : true,
  enumerable : true,
  configurable : true,
  get: function () {},
  set: function () {}
});
```
在 descriptor 中不能 同时设置访问器 (get 和 set) 和 wriable 或 value，否则会错，就是说想用(get 和 set)，就不能用（wriable 或 value中的任何一个）

##### Proxy [Proxy - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
[es6 Proxy - 阮一峰](https://es6.ruanyifeng.com/#docs/proxy)
```
const proxy = new Proxy({}, {
  get: function(target, key, receiver) {
    return receiver;
  }
});

const d = Object.create(proxy);
d.a === d // true
//d对象本身没有a属性，所以读取d.a的时候，会去d的原型proxy对象找。
//这时，receiver就指向d，代表原始的读操作所在的那个对象
});
```
get和set方法中的receiver参数参考[Proxy get中的receiver问题](https://github.com/ruanyf/es6tutorial/issues/669)
#####小结
+ Object.defineProperty是直接对原始对象进行操作并返回，Proxy是进行一个类似多例的操作，不会影响原始对象
+ Object.defineProperty的属性较少，Proxy对数据劫持对handler方法较多，但两者都有最基础的get和set
### Object.defineProperty与Proxy的缺点 
##### Object.defineProperty的缺点
因为defineProperty的属性限制，导致了三个问题
1、无法监听到数组的长度变化
2、由于只能劫持对象的属性，对于复杂对象需要对每个属性进行深度遍历
3、由于只能劫持对象的属性，对象属性有新增时，需要将对象的所有属性都进行遍历进行监听
[为什么defineProperty不能检测到数组长度的“变化”](https://juejin.im/post/5b0d0212f265da08da29e50f)

数组的length属性被初始化为如下
configurable为false也就是说length属性不能修改，不能删除，所以想我们想要通过get/set方法来监听length属性是不可行的
``` js
{
  writable: true,
  enumerable: false,
  configurable: false,
}
```

验证对象新增的属性在definproperty中能否被监听
```
let person = Object.defineProperty({age: 20}, 'name', {
    get: function() {
        console.log('get!!!');
        return name;
    },
    set: function(newName) {
        console.log('set!!!');
        name = newName;
    },
    enumerable: true,
    configurable: true
});
person.name; // 'get!!!'  'piers'
person.name = 'zhangpeng' // 'set!!!'  'zhangpeng'
person.age; //   'piers' 此时不会被get监听
person.age = '30'; // '30' 此时不会被set监听
```
##### Proxy的缺点
+ 由于是es6的特性，所以存在浏览器兼容性问题

##### 小结
+ Object.defineProperty有三个缺点
  1、无法监听到数组的长度变化
  2、由于只能劫持对象的属性，对于复杂对象需要对每个属性进行深度遍历
  3、由于只能劫持对象的属性，对象属性有新增时，需要将对象的所有属性都进行遍历进行监听
+ Proxy有一个缺点
  1、存在浏览器兼容性问题

###Object.defineProperty在Vue和MobX中造成的问题
由于Object.defineProperty的三个缺点，在Vue中有如下问题
```
// 在Vue中这种数组操作和对象操作，不会被Vue监听到
let vm1 = new Vue({
  data: {
     list: [1, 2, 3, 4]
  }
})
let vm2 = new Vue({
  data: {
    a: 1
  }
})
vm1.list[index] = newValue // 非响应式的
vm1.list.length = newLength  // 非响应式的
vm2.a = 2 // `vm.a` 是响应式的
vm2.b = 2 // `vm.b` 是非响应式的
```
对于Object.defineProperty的三个缺点，我们一次来看Vue和MobX是如何进行处理的
######1、无法监听数组长度属性变化的问题
  + Vue重写了push、pop、shift、unshift、splice、sort、reverse这七个数组方法，是的这些方法可以响应式，参考[Vue文档说明](https://cn.vuejs.org/v2/guide/list.html#%E6%95%B0%E7%BB%84%E6%9B%B4%E6%96%B0%E6%A3%80%E6%B5%8B)
  + MobX则是会在定义的时候，默认把类数组长度预留999个单位
这样其实不会对性能有损耗，因为js中对数据的遍历除了for循环还有forEach、map、filter、some等，除了for循环外(for,for...of)，其他的遍历都是对键值的遍历
```
let arr = [1];
arr[1000] = 1;
function a () {
  console.time();
  for(let i = 0; i < arr.length; i++) {
    console.log(arr[i]);
  }
}
function b () {
  console.time();
  arr.forEach((item) => {
    console.log(item);
  })
}
a(); //default: 567.1669921875ms
b(); //default: 0.81982421875ms
```
###### 2、需要对对象的深层属性或者新增属性进行遍历，达到监听的目的
+ Vue提供了[Vue.set(object, propertyName, value)](https://cn.vuejs.org/v2/guide/reactivity.html#%E6%A3%80%E6%B5%8B%E5%8F%98%E5%8C%96%E7%9A%84%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)方法向嵌套对象添加响应式property
或者用[watch的deep参数](https://cn.vuejs.org/v2/api/#vm-options)监听对象上深层属性的变化
+ MobX一样，需要遍历对象的所有属性进行修改属性，只不过MobX没有帮你去做这个深层遍历，而是需要你在使用MobX的@observable的时候提前将需要双向绑定的数据提前定义出来，或者强制将对象强制更新[关于在MobX中如何observe观察深层的数据变动](https://github.com/ckinmind/ReactCollect/issues/97)

### 总结
+ Object.defineProperty与Proxy的使用上的区别
  1、Object.defineProperty是直接对原始对象进行操作并返回，Proxy是进行一个类似多例的操作，不会影响原始对象
  2、Object.defineProperty的属性较少，Proxy对数据劫持对handler方法较多，但两者都有最基础的get和set，可以将Proxy看成是Object.defineProperty的加强版
+ Object.defineProperty与Proxy的缺点
  1、Object.defineProperty无法监听到数组的长度变化
  2、Object.defineProperty由于只能劫持对象的属性，对于复杂对象需要对每个属性进行深度遍历
  3、Object.defineProperty由于只能劫持对象的属性，对象属性有新增时，需要将对象的所有属性都进行遍历进行监听
  4、Proxy存在浏览器兼容性问题
+ Object.defineProperty在Vue和MobX中造成的问题和解决办法
  1、Vue通过重写数据的七个方法，MobX通过提前将类数组声明为长度999，避免无法监听数组长度的问题
  2、Vue和MobX都是用遍历的方式来监听深层对象属性和新增的属性