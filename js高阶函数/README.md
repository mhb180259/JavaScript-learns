JavaScript高阶函数
===
定义
---
    至少满足下列条件之一的函数
        函数可以作为参数被传递；
        函数可以作为返回值输出；
示例1
---
```javascript
var Moqi = function(p1){
    return  function (p2){
        return function(p3){
            return p1 + ',' + p2 + ' ' +p3;
        }
    };
};
Moqi('Hello')('World')('Phodal')
```
```javascript
var Moqi = function(p1){
    var self = this;
    function fd(p2) {
        this.add1 = function (p3) {
            return p1 + ',' + p2 + ' ' + p3;
        };
    }
    self.add =  function (p2){
        fd.call(this, p2);
        return this.add1;
    };
    return self.add;
};
```
示例2
---
```javascript
add = function(a,b){
    return a + b;
};
function math(func,array){
    return func(array[0],array[1]);
}
console.log(math(add,[1,2]));
```
示例3(map/reduce)
---
```javascript
// 数组累加求和
var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x + y;
}); // 25

// 利用map和reduce操作实现一个parseInt()函数
function string2int(s) {
// 因为字符串没有map方法，所以使用Array.prototype.map方法
var n = Array.prototype.map.call(s, function(x) {
  return x - '0'
}); 
var m = n.reduce(function(x,y){
    return x*10+y;
});
return m;
}
```
示例4(filter)
---
```javascript
var arr = ['A', 'B', 'C'];
var r = arr.filter(function (element, index, self) {
    console.log(element); // 依次打印'A', 'B', 'C'
    console.log(index); // 依次打印0, 1, 2
    console.log(self); // self就是变量arr
    return true;
});
// 去除Array的重复元素
var r, arr = ['apple', 'strawberry', 'banana', 'pear', 'apple', 'orange', 'orange', 'strawberry'];

r = arr.filter(function (element, index, self) {
    return self.indexOf(element) === index;
});
```

示例5(闭包)
---
* 定义

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;闭包某种程度上就是函数的内部函数，可以引用外部函数的局部变量。<br>
当外部函数退出后，如果内部函数依旧能被访问到，那么内部函数所引用<br>
的外部函数的局部变量就也没有消失，该局部变量的生存周期就被延续。<br>
```javascript
    document.addEventListener('DOMContentLoaded',function(){
    var divs=document.getElementsByTagName('div');
        console.log(divs);
        for (var i = 0; i < divs.length; i++) {
            divs[i].onclick=function(){
                alert(i);
            }
        };    
    },false);
    // 或者
    var divs=document.getElementsByTagName('div');
        //console.log(divs);
        for (var i = 0; i < divs.length; i++) {
            divs[i].onclick=function(){
                (function(i){
                    alert(i);
                })(i);
            }
        };    
    },false);
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因为div节点的Onclick事件是异步触发的，当事件被触发的时候，for循环早已结束，<br>
此时变量i的值已经是循环结束时候的2；如果想要达到想要的效果，需要采用闭包的形式：
```javascript
    var divs=document.getElementsByTagName('div');
        //console.log(divs);
        for (var i = 0; i < divs.length; i++) {
            (function(i){
                divs[i].onclick=function(){
                    alert(i);
                }
            })(i);
        };    
    },false);
```
示例6(ajax异步请求：作为参数传递)
---
```javascript
// callback为待传入的回调函数
var getUserInfo = function(userId, callback) {
     $.ajax("http://xxx.com/getUserInfo?" + userId, function(data) {
        if (typeof callback === "function") {
            callback(data);
        }
    });
}

getUserInfo(13157, function(data) {
    alert (data.userName);
});
```
示例7(作为返回值)
---
```javascript
//判断数据的类型
var Type = {};
for (var i = 0, type; type = ['String', 'Array', 'Number'][i++];) {
    (function(type) {
        Type['is' + type] = function(obj) {
            return Object.prototype.toString.call(obj) === '[object '+ type +']';
           }
       })(type)
};

Type.isArray([]);     // 输出：true
Type.isString("str");     // 输出：true
// 单例模式
var getSingle = function(fn) {
    var ret;
    return function() {
        return ret || (ret = fn.apply(this, arguments));
    };
};
```
示例8(AOP)
---
* AOP概念

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AOP（面向切面编程）的主要作用是把一些跟核心业务逻辑模块无关的功能抽离<br>
出来，这些跟业务逻辑无关的功能通常包括日志统计、安全控制、异常处理等。把这<br>
些功能抽离出来之后，再通过“动态织入”的方式掺入业务逻辑模块中。这样做的好处<br>
首先是可以保持业务逻辑模块的纯净和高内聚性，其次是可以很方便地复用日志统计<br>
等功能模块。
```javascript
Function.prototype.before = function(beforefn) {
    var __self = this;    // 保存原函数的引用
    return function() {    // 返回包含了原函数和新函数的"代理"函数
        beforefn.apply(this, arguments);     // 执行新函数，修正this
        return __self.apply(this, arguments);    // 执行原函数
    }
};

Function.prototype.after = function(afterfn) {
    var __self = this;
    return function() {
        var ret = __self.apply(this, arguments);
        afterfn.apply(this, arguments);
        return ret;
    }
};

var func = function() {
    console.log(2);
};

func = func.before(function() {
    console.log(1);
}).after(function() {
    console.log(3);
});

func();
```

示例9(currying)
---
* currying概念

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;currying（函数柯里化），又称部分求值。一个currying的函数首先会接受一些参数，接受<br>
了这些参数之后，该函数并不会立即求值，而是继续返回另外一个函数，刚才传入的参数在函数<br>
形成的闭包中被保存起来。待到函数被真正需要求值的时候，之前传入的所有参数都会被一次性<br>
用于求值。

```javascript
// 通用currying函数，接受一个参数，即将要被currying的函数
var currying = function(fn) {
    var args = [];
    return function() {
        if (arguments.length === 0) {
            return fn.apply(this, args);
        } else {
            // [].push.apply(args, arguments);
            Array.prototype.push.apply(args,arguments)
            return arguments.callee;
        }
    }
};

// 将被currying的函数
var cost = (function() {
    var money = 0;
    return function() {
        for (var i = 0, l = arguments.length; i < l; i++) {
            money += arguments[i];
        }
        return money;
    }
})();

var cost = currying( cost );    // 转化成currying函数

cost( 100 );    // 未真正求值
cost( 200 );    // 未真正求值
cost( 300 );    // 未真正求值

console.log (cost());     // 求值并输出：600
```
示例10(uncurrying)
---
* uncurrying概念

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;uncurrying的目的是将泛化this的过程提取出来，将fn.call或者fn.apply抽象成通用的函数。

```javascript
// uncurrying实现
Function.prototype.uncurrying = function() {
    var self = this;
    return function() {
        return Function.prototype.call.apply(self, arguments);
    }
};

// 将Array.prototype.push进行uncurrying，此时push函数的作用
// 就跟Array.prototype.push一样了，且不仅仅局限于只能操作array对象。
var push = Array.prototype.push.uncurrying();

var obj = {
    "length": 1,
    "0": 1
};

push(obj, 2);
console.log(obj);   // 输出：{0: 1, 1: 2, length: 2}
```
示例11(函数节流)
---

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当一个函数被频繁调用时，如果会造成很大的性能问题的时候，这个时候可以考虑函数节流，<br>
降低函数被调用的频率。throttle函数的原理是，将即将被执行的函数用setTimeout延迟一段时间<br>
执行。如果该次延迟执行还没有完成，则忽略接下来调用该函数的请求。throttle函数接受2个参数<br>
，第一个参数为需要被延迟执行的函数，第二个参数为延迟执行的时间。<br>

```javascript
var throttle = function(fn, interval) {
    var __self = fn,    // 保存需要被延迟执行的函数引用
        timer,      // 定时器
        firstTime = true;    // 是否是第一次调用

    return function() {
        var args = arguments,
            __me = this;

        if (firstTime) {    // 如果是第一次调用，不需延迟执行
            __self.apply(__me, args);
            return firstTime = false;
        }

        if (timer) {    // 如果定时器还在，说明前一次延迟执行还没有完成
            return false;
        }

        timer = setTimeout(function() {  // 延迟一段时间执行
            clearTimeout(timer);
            timer = null;
            __self.apply(__me, args);
        }, interval || 500 );
    };
};

window.onresize = throttle(function() {
    console.log(1);
}, 500 );
```

示例12(分时函数)
---

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当一次的用户操作会严重地影响页面性能，如在短时间内往页面中大量添加DOM节点<br>
显然也会让浏览器吃不消，我们看到的结果往往就是浏览器的卡顿甚至假死。这个问题的<br>
解决方案之一是下面的timeChunk函数，timeChunk函数让创建节点的工作分批进行，比如<br>
把1秒钟创建1000个节点，改为每隔200毫秒创建8个节点。<br>

```javascript
var timeChunk = function(ary, fn, count) {
    var t;

    var start = function() {
        for ( var i = 0; i < Math.min( count || 1, ary.length ); i++ ){
            var obj = ary.shift();
            fn( obj );
        }
     };

     return function() {
        t = setInterval(function() {
          if (ary.length === 0) {  // 如果全部节点都已经被创建好
              return clearInterval(t);
          }
          start();
        }, 200);    // 分批执行的时间间隔，也可以用参数的形式传入
    };
};
```

示例12(惰性加载函数)
---

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在Web开发中，因为浏览器之间的实现差异，一些嗅探工作总是不可避免。比如<br>
我们需要一个在各个浏览器中能够通用的事件绑定函数addEvent，常见的写法如下：<br>

```javascript
// 方案一：
var addEvent = function(elem, type, handler) {
    if (window.addEventListener) {
       return elem.addEventListener(type, handler, false);
    }
    
    if (window.attachEvent) {
          return elem.attachEvent('on' + type, handler);
    }
};
// 缺点：当它每次被调用的时候都会执行里面的if条件分支，
// 虽然执行这些if分支的开销不算大，但也许有一些方法可以
// 让程序避免这些重复的执行过程。

// 方案二：
var addEvent = (function() {
    if (window.addEventListener) {
        return function(elem, type, handler) {
            elem.addEventListener(type, handler, false);
        }
    }
    if (window.attachEvent) {
        return function(elem, type, handler) {
            elem.attachEvent('on' + type, handler);
        }
    }
})();
// 缺点：也许我们从头到尾都没有使用过addEvent函数，这样看来，一开始的
// 浏览器嗅探就是完全多余的操作，而且这也会稍稍延长页面ready的时间。

// 方案三：
var addEvent = function(elem, type, handler) {
    if (window.addEventListener) {
       addEvent = function(elem, type, handler) {
           elem.addEventListener(type, handler, false);
       }
    } else if (window.attachEvent) {
        addEvent = function(elem, type, handler) {
            elem.attachEvent('on' + type, handler);
        }
    }
    addEvent(elem, type, handler);
};
// 此时addEvent依然被声明为一个普通函数，在函数里依然有一些分支判断。
// 但是在第一次进入条件分支之后，在函数内部会重写这个函数，重写之后的
// 函数就是我们期望的addEvent函数，在下一次进入addEvent函数的时候，
// addEvent函数里不再存在条件分支语句。
```