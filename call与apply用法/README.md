call与apply用法
===
定义
---
        1 调用一个对象的一个方法，以另一个对象替换当前对象。
        2 劫持另外一个对象的方法，继承另外一个对象的属性。
* call 语法：call(Object,arg1,arg2,...)
* apply 语法：apply(Object,arguments)

说明
---
Function.apply(obj,args)方法能接收两个参数  
obj：这个对象将代替Function类里this对象  
args：这个是数组，它将作为参数传给Function


示例1
---
在调用apply方法的时候,第一个参数是对象(this), 第二个参数是一个数组集合,它可以将一个数组默认的转换为一个参数列表

Math.max 可以实现得到数组中最大的一项: Math.min.apply(null,array)

示例2
---
```javascript
function add(a,b) {  
    alert(a+b);  
}  
function sub(a,b) {  
    alert(a-b); 
}    
add.call(sub,3,1);
// 在sub的上下文中执行add函数
```

示例3
---
```javascript
function Animal(){    
    this.name = "Animal";    
    this.showName = function(){    
        alert(this.name);    
    }    
}    
function Cat(){    
    this.name = "Cat";    
}    
var animal = new Animal();    
var cat = new Cat();    
//结果为"Cat"    
animal.showName.call(cat,","); 
```
示例4 (继承)
---
```javascript
function Animal(name){      
    this.name = name;      
    this.showName = function(){      
        alert(this.name);      
    }      
}      
function Cat(name){    
    Animal.call(this, name);    
}      
var cat = new Cat("Black Cat");     
cat.showName(); 
```
多重继承
---
```javascript
function Class10() {  
    this.showSub = function(a,b) {  
        alert(a-b);  
    }  
}  
function Class11() {  
    this.showAdd = function(a,b) {  
        alert(a+b);  
    }  
}  
function Class2() {  
    Class10.call(this);  
    Class11.call(this);  
} 
```
Function.prototype.apply.call使用
---
```javascript
var console = window.console || {log: function () {}};   
var log = console.log;  
console.log = function(tips,message){   
   Function.prototype.apply.call(log, console, arguments);   
} 
console.log([1,2,3]);  
```
分析
>>首先可以将Function.prototype.apply看成一个整体-->FunctionApply<br>
  FunctionApply.call(log,console,arguments)<br>
  那么将此句翻译一下log.FunctionApply(console,arguments);<br>
  然后再翻译一下,就是一个普通方法调用了console.log(arguments);<br>
  
总结
>>Function.prototype.apply.call  等同于Function.prototype.call.call<br>
Function.prototype.call.apply  等同于 Function.prototype.apply.apply<br>

>>XXX可以是call或者是apply,child一定是parent可指向的对象<br>
Function.prototype.XXX.call(child,parent,arguments||array);<br>
Function.prototype.XXX.apply(child,[parent,arguments||array]);<br>

参考链接<br>
[链接一](http://uule.iteye.com/blog/1158829)<br>
[链接二](http://blog.csdn.net/business122/article/details/8000676)<br>
[链接三](http://zhangzhaoaaa.iteye.com/blog/2246733)<br>
