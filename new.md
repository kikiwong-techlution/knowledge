### 参考来源https://github.com/mqyqingfeng/Blog

#### 从原型到原型链  
- `prototype`  
每一个函数都有一个`prototype`属性。函数的`prototype`属性指向了一个对象，该对象是调用该构造函数而创建的实例的原型。每个对象都会从原型“继承”属性。
- `__proto__`  
每个JavaScript对象都会有一个属性，叫`__proto__`，这个属性会指向该对象的原型。
    ```javascript
    function Person(){}
    var person = new Person()
    person.__proto__ === Person.prototype;  //true
    ```
- `constructor`  
每个原型都会有一个`constructor`属性指向关联的构造函数
    ```javascript
    function Person(){}
    var person = new Person()
    Person === Person.prototype.constructor;  //true
    ```
- 实例与原型  
当读取实例的属性时，如果找不到，就会查找与对象关联的原型中属性；如果还找不到，就去找原型的原型，一直到最顶层为止。  
原型的原型是`Object.prototype`,原型对象是通过`Object`构造函数生成的。  
`Object.prototype`没有原型，会指向null。
- 原型链  
相互关联的原型组成的链状结构就是原型链
#### 词法作用域和动态作用域
#### 执行上下文栈
#### 变量对象
#### 作用域链
#### **ECMAScript规范解读this**
#### 执行上下文
#### 闭包
#### 参数按值传递
#### **call和applu模拟实现**
#### bind的模拟实现
#### **new模拟实现**
#### 数组对象与arguments
#### 创建对象多种方式及优缺点
#### 继承多种方式及优缺点
#### 防抖
#### 节流
#### 数组去重
#### 类型判断
#### 深浅拷贝
#### 从零实现jQuery的extend
#### 数组最大值和最小值
#### 数组扁平化
#### 在数组中查找指定元素
#### jQuery通用遍历方法each的实现
#### 判断两个对象相等
#### 函数柯里化
#### 偏函数
#### 惰性函数
#### 函数组合
#### 函数记忆
#### 递归
#### 乱序
#### 解读v8排序源码
