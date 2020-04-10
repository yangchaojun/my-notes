#### 从调用场景来看this的指向

- 作为对象调用，this指向该对象
- 作为全局函数调用，this指向全局对象（window 或 global）
- 作为构造函数调用，this指向当前实例对象
- 作为call与apply调用，this指向当前传入的对象

#### 修改this的指向

1. call

   > `call()`方法在使用一个指定的this值和若干个指定的参数值的前提下调用某个函数或方法

   由此可以得出call函数的特点：

   1. 可以传入1个及以上的参数，但第一个参数必须是对象或null
   2. 可以有返回值，与普通函数相同

   模拟call方法：

   ```javascript
   Function.prototype.call2 = function (context) {
       var context = context || window; // this 参数可以传 null，当为 null 的时候，视为指向 window
       context.fn = this;
   
       var args = [];
       // 传入的参数并不确定
       for(var i = 1, len = arguments.length; i < len; i++) {
           args.push('arguments[' + i + ']');
       }
   
       var result = eval('context.fn(' + args +')');
   
       delete context.fn
       return result; // 函数是可以有返回值
   }
   
   // 测试一下
   var value = 2;
   
   var obj = {
       value: 1
   }
   
   function bar(name, age) {
       console.log(this.value);
       return {
           value: this.value,
           name: name,
           age: age
       }
   }
   
   bar.call2(null); // 2
   
   console.log(bar.call2(obj, 'kevin', 18));
   // 1
   // Object {
   //    value: 1,
   //    name: 'kevin',
   //    age: 18
   // }
   ```

   

2. apply

   apply效果与call类似，但是传的第二个参数是一个数组

   apply模拟实现

   ```javascript
   Function.prototype.apply = function (context, arr) {
       var context = Object(context) || window;
       context.fn = this;
   
       var result;
       if (!arr) {
           result = context.fn();
       }
       else {
           var args = [];
           for (var i = 0, len = arr.length; i < len; i++) {
               args.push('arr[' + i + ']');
           }
           result = eval('context.fn(' + args + ')')
       }
   
       delete context.fn
       return result;
   }
   ```

   

