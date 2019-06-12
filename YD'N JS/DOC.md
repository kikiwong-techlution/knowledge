#### 隐式产生屏蔽
```javascript
var anotherObj={
    a:2
};
// Object.defineProperty(anotherObj,"a",{
//     writable:false
// });
var myObj=Object.create(anotherObj);

anotherObj.a;  //2
myObj.a;   //2
anotherObj.hasOwnProperty("a");  //true
myObj.hasOwnProperty("a");  //false
myObj.a++;   //隐式屏蔽！！
anotherObj.a; //2
myObj.a;   //3
myObj.hasOwnProperty("a"); //true
```
#### 混入

* 显式混入
```javascript
// 非常简单的mixin（）例子
function mixin(sourceObj, targetObj) {
    for(var key in sourceObj){
        //只会在不存在的情况下复制
        if(!(key in targetObj)){
            targetObj[key]=sourceObj[key];
        }
    }
    return targetObj;
}
var Vehicle={
    engines:1,
    ignition:function () {
        console.log("Turning on my engines.");
    },
    drive:function () {
        this.ignition();
        console.log("Steering and moving forward!");
    }
};
var Car=mixin(Vehicle,{
    wheels:4,
    drive:function () {
        Vehicle.drive.call(this);
        console.log(
            "Rolling on all" + this.wheels + "wheels!"
        );
    }
});
```

* 寄生混入：既是显式又是隐式的
```javascript
//传统的JavaScript类：Vehicle
function Vehicle() {
    this.engines=1;
}
Vehicle.prototype.ignition=function () {
    console.log("Turning on my engines.");
};
Vehicle.prototype.drive=function () {
    this.ignition();
    console.log("Steering and moving forward!");
};

//寄生类：Car
function Car() {
    var car = new Vehicle();

    car.wheels=4;
    //保存到Vehicle：：drive（）的特殊引用
    var vehDrive=car.drive;
    //重写Vehicle：：drive（）
    car.drive=function () {
        vehDrive.call(this);
        console.log(
            "Rolling on all " + this.wheels + " wheels!"
        );
    };
    return car;
}
var myCar=new Car();  //没有使用新对象，而是返回了car对象，所以也可以不使用new关键字调用Car（）
myCar.drive();
```

* 隐式混入
```javascript
var Something={
    cool:function () {
        this.greeting="Hello World";
        this.count=this.count ? this.count + 1 :1;
    }
};
Something.cool();
Something.greeting;
Something.count;

var Another={
    cool:function () {
        //隐式把Something混入Another
        Something.cool.call(this);
    }
};
Another.cool();
Another.greeting;
Another.count;  //count不是共享状态

```

#### 实现深拷贝

* 递归去复制所有层级属性
````javascript
function deepClone(obj) {
    let objClone=Array.isArray? [] : {};
    if(obj && typeof obj === "object"){
        for(key in obj){
            if(obj.hasOwnProperty(key)){
                if(obj[key] && typeof obj[key] === "object"){
                    objClone[key]=deepClone(obj[key])
                }else{
                    objClone[key]=obj[key];
                }
            }
        }
    }
    return objClone;
}
let arr1=[1,2,[1,2],4,5];
let arr2=deepClone(arr1);
arr1[2]=[2,3];
console.log(arr1,arr2);
````

* 利用JSONstringify和parse 或者 依赖JQ库
````javascript
function deepClone(obj){
    let _obj=JSON.stringify(obj), objClone=JSON.parse(_obj);
    return objClone;
}


let arr1=[1,2,[1,2],4,5];
let arr2=deepClone(arr1);
let arr3=$.extend(true,[],arr1);  //利用JQ库的.extend

arr1[0]=2;
arr1[2][0]=4;
arr2[2][0]=122;
console.log(arr1,arr2,arr3);
````
#### getter\setter
````javascript
 var myObj={
     get a(){
         return this._a_;
     },
     set a(val){
         this._a_=val*2;
     }
 };
 Object.defineProperty(myObj,"b",{
     get:function () {
         return this.a*2;
     },
     enumerable:true
 });
 console.log(myObj.a);
 console.log(myObj.b);
````
     
#### 现代的模块机制
````javascript
var MyModules = (function () {
     var modules ={};
     function define(name, deps, imps) {n
         for(var i=0;i<deps.length;i++){
             deps[i]=modules[deps[i]];
         }
         modules[name]=imps.apply(imps,deps);
     }
     function get(name) {
         return modules[name];
     }
     return{
         define: define,
         get: get
     };
 })();
 
 MyModules.define("bar",[],function () {
     function hello(who) {
         return "Let me introduce:" + who;
     }
     return{
         hello: hello
     };
 });
 MyModules.define("foo",["bar"],function (bar) {
     var hungry = "hippo";
     function awesome() {
         console.log(bar.hello(hungry).toUpperCase());
 
     }
     return{
         awesome: awesome
     };
 });
 var bar=MyModules.get("bar");
 var foo=MyModules.get("foo");
 console.log(bar.hello("hippo"));
 foo.awesome();
````
#### 原型风格，关联两个对象
````javascript
//ES6之前需要抛弃默认的Bar.prototype
Bar.prototype=Object.create(Foo.prototype);

//ES6开始可以直接修改现有的Bar.prototype
Object.setPrototypeOf(Bar.prototype,Foo.prototype);


function Foo(name) {
    this.name=name;
}
Foo.prototype.myName=function () {
    return this.name;
};
function Bar(name,label) {
    Foo.call(this,name);
    this.label=label;
}

Bar.prototype=Object.create(Foo.prototype);
Bar.prototype.myLabel=function () {
    return this.label;
};
var a=new Bar("a","obj a");
console.log(a.myName());  //"a"
console.log(a.myLabel());  //"obj a"
````
#### 原型风格，关联两个对象
````javascript
 function Foo(name) {
     this.name=name;
 }
 Foo.prototype.myName=function () {
     return this.name;
 };
 function Bar(name,label) {
     Foo.call(this,name);
     this.label=label;
 }
 
 Bar.prototype=Object.create(Foo.prototype);
 Bar.prototype.myLabel=function () {
     return this.label;
 };
 ````
 #### 误解
 * 荒谬的代码，试图在"类"的角度使用instanceof 来判断两个对象的关系
 * 但是实际上o1并没有继承F，也不是由F构造的，这是错误的！！！
 ````javascript
 function isRelatedTo(o1,o2) {
     function F() {}   //声明一次性函数F
     F.prototype=o2;   //将其.prototype重新赋值并指向对象o2
     return o1 instanceof F;    //然后判断o1是否是F的一个实例
 }
 var a={};
 var b=Object.create(a);
 isRelatedTo(b,a);   //true
 ````
 