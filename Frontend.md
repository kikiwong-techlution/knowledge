#### new 的实现原理

1）创建一个空对象，构造函数中的 this 指向这个空对象  
2）这个新对象被执行[[prototype]]连接  
3）执行构造函数方法，属性和方法呗添加到 this 引用的对象中  
4）如果构造函数中没有返回其他对象，那么返回 this，即创建的这个新对象；否则返回构造函数中返回的对象

```javascript
function _new() {
  let target = {};
  let [constructor, ...args] = [argument];
  target.__proto__ = constructor.prototype;
  let result = constructor.apply(target, args);
  if (result && (typeof result == "object" || typeof result == "function")) {
    return result;
  }
  return target;
}
```
### HTTP 相关
- nodejs
```javascript
var http=require('http');
http.createServer(function(req,res){
	res.writeHead(200,{'Content-Type':'text/plain','Access-Control-Allow-Origin': '*'});
	res.write('Hello world.');
	res.end();
}).listen(3000);
```
- 手写一个 ajax

```javascript
let ajax = function() {
  let xhr;
  if (window.XMLHttpRequest) {
    xhr = new XMLHttpRequest();
    if (xhr.overrideMimeType) {
      xhr.overrideMimeType("text/xml");
    }
  } else if (window.ActiveXObject) {
    xhr = new ActiveXObject();
    let activeName = ["MSXML2.XMLHTTP", "Microsoft.XMLHTTP"];
    for (let i = 0; i < activeName.length; i++) {
      try {
        xhr = new ActiveXObject(activeName[i]);
        if (xhr) {
          break;
        }
      } catch (e) {
        console.log(e);
      }
    }
  }
  return xhr;
};
ajax.open("GET", url, true);

ajax.open("POST", url, true);
ajax.setRequestHeader(
  "Content-Type:application/x-www-form-urlencoded;charset=utf-8"
);

//响应主体
ajax.onreadystatechange = function() {
  if (ajax.readystate == 4) {
    if (ajax.status == 200) {
      alert(ajax.responseText);
    }
  }
};

ajax.send(null); //GET就不用发送内容

ajax.send("name=movbenr"); //POST可以发送请求条件
```

- ajax 请求具体

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="x-ua-compatible" content="ie=edge" />
    <meta http-equiv="Access-Control-Allow-Origin" content="*" />

    <title>Document</title>
    <style type="text/css">
      #btn {
        width: 100px;
        height: 100px;
      }
    </style>
  </head>

  <body>
    <button id="ajaxButton" type="button">Make a request</button>
    <!-- <div>
        <label>Your name:
            <input type="text" id="ajaxTextbox" />
        </label>
        <span id="ajaxButton" style="cursor: pointer; text-decoration: underline">
            Make a request
        </span>
    </div> -->

    <script>
      (function() {
        var httpRequest;

        document
          .getElementById("ajaxButton")
          .addEventListener("click", makeRequest);
        // document.getElementById("ajaxButton").onclick = function () {
        //     var userName = document.getElementById("ajaxTextbox").value;
        //     makeRequest('test.php', userName);
        // };

        function makeRequest() {
          httpRequest = new XMLHttpRequest(); //创建XMLHttp实例
          if (!httpRequest) {
            alert("Giving up :( Cannot create an XMLHTTP instance");
            return false;
          }
          httpRequest.onreadystatechange = alertContents;
          //请求方法POST
          // httpRequest.open('POST', url);
          // httpRequest.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
          // httpRequest.send('userName=' + encodeURIComponent(userName));

          httpRequest.onreadystatechange = alertContents; //告诉XMLHttp请求对象哪个JavaScript函数将处理响应
          httpRequest.open("GET", "http://127.0.0.1:3000/"); //通过调用HTTP请求对象的open()和send()方法来实际发出请求
          httpRequest.send();
        }

        // function alertContents() {
        //     if (httpRequest.readyState === XMLHttpRequest.DONE) {
        //         if (httpRequest.status === 200) {
        //             var response = JSON.parse(httpRequest.responseText);
        //             alert(response.computedString);
        //         } else {
        //             alert('There was a problem with the request.');
        //         }
        //     }
        // }
        function alertContents() {
          try {
            if (httpRequest.readyState === XMLHttpRequest.DONE) {
              //状态值对应4
              if (httpRequest.status === 200) {
                //通过检查200 OK响应代码来区分成功和不成功的AJAX调用
                //访问数据：httpRequest.responseText - 将服务器响应作为一串文本返回
                alert(httpRequest.responseText);

                //httpRequest.responseXML- 将响应作为XMLDocument可以使用JavaScript DOM函数遍历的对象返回
                // var xmldoc = httpRequest.responseXML;
                // var root_node = xmldoc.getElementsByTagName('root').item(0);
                // alert(root_node.firstChild.data);
              } else {
                alert("There was a problem with the request.");
              }
            }
          } catch (e) {
            alert("Caught Exception: " + e.description);
          }
        }
      })();
    </script>
  </body>
</html>
```

### 数组去重！！各种解法

- **数组排序，去重并计算每个数出现的次数。**

```javascript
var arr = [1, 4, 5, 9, 10, 4, 5, 7, 7, 7, 7, 8];
var res = [];
var map = new Map();
for (let i = 0; i < arr.length; i++) {
  if (res.indexOf(arr[i]) == -1) {
    map.set(arr[i], 1);
    res.push(arr[i]);
  } else {
    map.set(arr[i], map.get(arr[i]) + 1);
  }
}
console.log(res, map);
```

- **删除数组的重复项 2**  
  给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素最多出现两次，返回移除后数组的新长度。
  不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成

```javascript
var removeDuplicates = function(nums) {
  let len = nums.length;
  for (let i = 0; i < len; i++) {
    for (let j = i + 1; j < len; j++) {
      if (nums[i] === nums[j] && nums[i] === nums[j + 1]) {
        nums.splice(j, 1);
        len--;
        j--;
      }
    }
  }
  return nums.length;
};
```

- **编写一个程序将数组扁平化去并除其中重复部分数据，最终得到一个升序且不重复的数组。**

```javascript
var handleArr = function(arr) {
  arr = arr.join(",").split(",");
  arr.sort((a, b) => a - b);
  //不在原数组操作
  let unique = new Set(arr);
  return Array.from(unique);
};
console.log(
  handleArr([
    [1, 2, 2],
    [3, 4, 5, 5],
    [6, 7, 8, 9, [11, 12, [12, 13, [14]]]],
    10
  ])
);
```

- **删除排序数组的重复项，要求在原数组上执行**

```javascript
//返回删除重复项的数组长度
//methond-1
var removeDuplicates = function(nums) {
  const result = nums.filter((el, index, self) => {
    return self.indexOf(el) === index;
  });
  return nums.splice(0, nums.length, ...result).length; // 删除该数组中的所有项目，替换成新数组中的项目
};
console.log(removeDuplicates([1, 2, 3, 3, 3, 4, 4]));

//method-2
var removeDuplicates = function(nums) {
  const map = new Map();
  for (let i = 0; i < nums.length; i++) {
    if (!map.has(nums[i])) {
      map.set(nums[i], nums[i]);
    } else {
      nums.splice(i, 1);
      i--;
    }
  }
  return nums.length;
};
console.log(removeDuplicates([1, 2, 4, 4, 5, 5]));

//method-3
var removeDuplicates = function(nums) {
  let len = nums.length;
  for (let i = 0; i < len; i++) {
    for (let j = i + 1; j < len; j++) {
      if (nums[i] === nums[j]) {
        nums.splice(j, 1);
        len--;
        j--;
      }
    }
  }
  return nums.length;
};
```

- **数组去重，返回去重后的数组**

```javascript
const arr = ["a", "bb", "22", "a", "yuci", "haha", "22"];

//ES6 Set
let unique = new Set(arr);
console.log(Array.from(unique));

// push()
let arr2 = [];
for (let i = 0; i < arr.length; i++) {
  if (arr2.indexOf(arr[i]) == -1) {
    arr2.push(arr[i]);
  }
}
console.log(arr2);

// splice()
let arr3 = [...arr];
let len = arr.length;
for (let i = 0; i < len; i++) {
  for (let j = i + 1; j < len; j++) {
    if (arr3[i] === arr3[j]) {
      arr3.splice(j, 1);
      len--;
      j--;
    }
  }
}
console.log(arr3);

//排序去除相邻重复元素
let arr4 = [...arr];
arr4.sort();
let res = [];
for (let i = 0; i < arr4.length; i++) {
  if (arr4[i] != arr4[i + 1]) {
    res.push(arr4[i]);
  }
}
console.log(res);
```

- **返回重复元素的数组**

```javascript
function duplicates(arr) {
  arr.sort();
  let res = [];
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] == arr[i + 1] && res.indexOf(arr[i]) == -1) {
      res.push(arr[i]);
    }
  }
  return res;
}

console.log(duplicates([1, 3, 4, 2, 3, 3, 4]));
```

#### ES6 Map 对象

Map 数据结构，类似于对象，也是键值对的集合，各种类型都可以当作键，即 Object 结构提供了"字符串-值"对应，Map 结构提供"值-值"对应，是一种更完善的 Hash 结构实现。

```javascript
const map = new Map();
const o = { p: "Hello,World" };
map.set(o, "content");
map.get(o); //'content'
map.has(o); //true
map.delete(o); //true
map.has(o); //false
```

- Map 结构原生提供三个遍历器生成函数和一个遍历方法。
  - `Map.prototype.keys()`：返回键名的遍历器。
  - `Map.prototype.values()`：返回键值的遍历器。
  - `Map.prototype.entries()`：返回所有成员的遍历器。
  - `Map.prototype.forEach()`：遍历 Map 的所有成员

```javascript
const map = new Map([["F", "no"], ["T", "yes"]]);

for (let key of map.keys()) {
  console.log(key);
}
// "F"
// "T"

for (let value of map.values()) {
  console.log(value);
}
// "no"
// "yes"

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
// "F" "no"
// "T" "yes"

// 或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"

// 等同于使用map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"
```

- Map 结构转为数组结构，比较快速的方法是使用扩展运算符`（...）`。

```javascript
const map = new Map([[1, "one"], [2, "two"], [3, "three"]]);

console.log([...map.keys()]);
// [1, 2, 3]

console.log([...map.values()]);
// ['one', 'two', 'three']

console.log([...map.entries()]);
// [[1,'one'], [2, 'two'], [3, 'three']]

console.log([...map]);
// [[1,'one'], [2, 'two'], [3, 'three']]
```

- 结合数组的 `map` 方法、`filter` 方法，可以实现 Map 的遍历和过滤（Map 本身没有 map 和 filter 方法）。

```javascript
const map0 = new Map()
  .set(1, "a")
  .set(2, "b")
  .set(3, "c");

const map1 = new Map([...map0].filter(([k, v]) => k < 3));
// 产生 Map 结构 {1 => 'a', 2 => 'b'}

const map2 = new Map([...map0].map(([k, v]) => [k * 2, "_" + v]));
// 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}
```

- Map 还有一个 `forEach` 方法，与数组的 forEach 方法类似，也可以实现遍历。forEach 方法还可以接受第二个参数，用来绑定 this。

```javascript
map.forEach(function(value, key, map) {
  console.log("Key: %s, Value: %s", key, value);
});

const reporter = {
  report: function(key, value) {
    console.log("Key: %s, Value: %s", key, value);
  }
};

map.forEach(function(value, key, map) {
  this.report(key, value); //回调函数的this，就指向reporter
}, reporter);
```

#### 如何让(a==1 && a==2 && a==3)为 true

- 利用隐式类型转换

```javascript
let a = {
  [Symbol.toPrimitive]: (function(hint) {
    let i = 1;
    return function() {
      return i++;
    };
  })()
};

console.log(a == 1 && a == 2 && a == 3); //true
```

- 利用数据劫持(Proxy/Object.defineProperty)

```javascript
let i = 1;
let a = new Proxy(
  {},
  {
    i: 1,
    get: function() {
      return () => this.i++;
    }
  }
);

console.log(a == 1 && a == 2 && a == 3); //true
```

- 数组的 toString 接口默认调用数组的 join 方法，重写 join 方法

```javascript
let a = [1, 2, 3];
a.join = a.shift;
console.log(a == 1 && a == 2 && a == 3); //true
```

#### 柯理化函数实现

```javascript
const curry = (fn, ...args) =>
  args.length < fn.length
    ? (...arguments) => curry(fn, ...args, ...arguments)
    : fn(...args);

function sumFn(a, b, c) {
  return a + b + c;
}
var sum = curry(sumFn);
console.log(sum(2)(3)(5)); //10
console.log(sum(2, 3)(5)); //10
```

#### 如何模拟实现 call？

```javascript
Function.prototype.call = function() {
  let [thisArg, ...args] = [...arguments];
  if (!thisArg) {
    //context is null or undefined
    thisArg = typeof window === "undefined" ? global : window;
  }
  //this指向是当前的func
  thisArg.func = this;
  let result = thisArg.func(...args);
  delete thisArg.func;
  return result;
};
```

#### 如何模拟实现 apply？

```javascript
Function.prototype.apply = function (thisArg, ret) {
    let result;
    if (!thisArg) {
        thisArg = typeof window === 'undefined' ? global : window;
    }
    thisArg.func = this;
    if (!ret) {
        //第二个参数是null/undefined
        result = thisArg.func();
    } else {
        result = thisArg.func(...ret);
    }
    delete thisArg.func;
    return result;
```

#### 深拷贝和浅拷贝的区别是什么？实现一个深拷贝  
对于复杂数据类型来说，浅拷贝只拷贝一层，而深拷贝是层层拷贝
- 深拷贝复制变量值，对于非基本类型的变量，则递归至基本变量后，再复制。  
深拷贝的对象与原来的对象是完全隔离的，互不影响，对一个对象的修改并不影响另一个对象。
- 浅拷贝是会将对象的每一个属性依次复制，但是当对象的属性值是引用类型时，实质复制的是其引用，当引用指向的值改变时也会跟着变化。  
浅拷贝可用`for in`、`Object.assign`、`扩展运算符[...]`、`Array.prototype.slice()`、`Array.prototype.concat()`等

- **实现深拷贝**  
	- 递归去复制所有层级属性

	```javascript
	function deepClone(obj) {
	  let objClone = Array.isArray ? [] : {};
	  if (obj && typeof obj === "object") {
	    for (key in obj) {
	      if (obj.hasOwnProperty(key)) {
		if (obj[key] && typeof obj[key] === "object") {
		  objClone[key] = deepClone(obj[key]);
		} else {
		  objClone[key] = obj[key];
		}
	      }
	    }
	  }
	  return objClone;
	}
	let arr1 = [1, 2, [1, 2], 4, 5];
	let arr2 = deepClone(arr1);
	arr1[2] = [2, 3];
	console.log(arr1, arr2);
	```

	- 利用 JSONstringify 和 parse 或者 依赖 JQ 库

	```javascript
	function deepClone(obj) {
	  let _obj = JSON.stringify(obj),
	    objClone = JSON.parse(_obj);
	  return objClone;
	}

	let arr1 = [1, 2, [1, 2], 4, 5];
	let arr2 = deepClone(arr1);
	let arr3 = $.extend(true, [], arr1); //利用JQ库的.extend

	arr1[0] = 2;
	arr1[2][0] = 4;
	arr2[2][0] = 122;
	console.log(arr1, arr2, arr3);
	```

