### 高频问题（24个）
##### typeof问题汇总
typeof能正确区分原始值吗

	不能。对于原始类型，除了null都可以正确判断；对于引用类型，除了function外，都会返回object
typeof的返回值有哪些

	Number、string、Boolean、undefined、object、function、symbol
	返回值是string格式
typeof function 会显示什么

	function
typeof为什么对null错误的显示

	遗留已久的BUG
    在 JS 的最初版本中使用的是 32 位系统
    为了性能考虑使用低位存储变量的类型信息
    000 开头代表是对象然而 null 表示为全零
    所以将它错误的判断为 object
typeof('abc')和 typeof 'abc'都是 string, 那么 typeof 是操作符还是函数？

	操作符
    这里的括号是进行分组而不是函数的调用

##### JavaScript的数据类型
Boolean
Number
String
undefined
null
Bigint

    ES2020 新增加，是比 Number 类型的整数范围更大
Symbol

    ES6 引入的一种新的原始值，表示独一无二的值，主要为了解决属性名冲突问题


##### == 的隐式转换规则
只需要值相等，不用类型相等

	null undefined在==下互相相等且自身相等
基本上都是转Number或Primitive


##### this指向
- 默认绑定:

        非严格模式下 this 指向全局对象，严格模式下 this 会绑定为 undefined
- 隐式绑定: 

        满足 XXX.fn() 格式，fn 的 this 指向 XXX。
        如果存在链式调用， this 永远指向最后调用它的那个对象。
        隐式绑定丢失：起函数别名，通过别名运行；函数作为参数会造成隐式绑定丢失
- 显式绑定: 

        通过 call/apply/bind 修改 this 指向
- new绑定: 

        通过 new 来调用构造函数，会生成一个新对象
        并且把这个新对象绑定为调用函数的 this 。
- 箭头函数绑定: 

        箭头函数没有 this ，它的 this 是通过作用域链查到外层作用域的 this
        且指向函数定义时的 this 而非执行时

##### 原型与原型链

prototype和constructor属性
一层层向上找，找到Object为止
```js
function User(){}
const u1=new User()
//u1._proto_->User.prototype
console.log(u1._proto_===User.prototype) //true
const u2=new User()
console.log(u1._proto_===u2._proto_) //true

Object._proto_===Function.prototype
//Object.prototype是Object构造函数的原型，处于原型链的顶端
Object.prototype.__proto__===null

Function.__proto__ --> Function.prototype
//Function函数不通过任何东西创建，JS引擎启动时，添加到内存中
```

##### 事件循环机制
主线程有个队列，这里面空了就会去事件队列读取回调函数，放回主线程执行，循环这个过程就是事件循环

	宏任务清空->微任务清空->宏任务
Promise定义部分为同步任务，回调部分为异步任务。Promise的优先级高于SetTimeout
宏任务、微任务
	他们有各自的任务队列

代码开始执行会创建一个全局调用栈，script作为宏任务执行
执行过程同步任务立即执行，异步任务根据类型分别注册到微任务队列和宏任务队列
同步任务执行完毕后查看微任务队列
如果存在微任务就把它们全部执行，包括微任务产生的新的微任务
如果没有微任务，查看宏任务队列，执行完宏任务，再查看微任务队列

##### 作用域与作用域链
除了函数内部和{}内部定义的变量，其余都属于全局作用域
ES6提供了块级作用域，目前局部作用域包含函数作用域和块级作用域

作用域是分层的，内部函数可以访问外部函数作用域里的变量，反之不可以(作用域链)
这一层找不到就会往外层找，直到window对象

##### 闭包
闭包是指有权访问另外一个函数作用域中的变量的函数
各种回调函数就体现了闭包的概念
可以把变量存在内存中，但会增加开销，要注意内存泄漏

##### 预编译
函数声明整体提升，变量声明提升

##### new 操作符做了哪些事情
创建一个空的简单 JavaScript 对象（即 {} ）
为步骤1新创建的对象添加属性 proto ，将该属性链接至构造函数的原型对象
将步骤1新创建的对象作为 this 的上下文 
如果该函数没有返回对象，则返回 this 


##### 什么是promise？实现一个简单的promise
Promise 是一个类，在执行这个类的时候会传入一个执行器，这个执行器会立即执行
Promise 会有三种状态

    Pending 等待
    Fulfilled 完成
    Rejected 失败
状态只能由 Pending --> Fulfilled 或者 Pending --> Rejected
    且一但发生改变便不可二次修改
Promise 中使用 resolve 和 reject 两个函数来更改状态
then 方法内部做事情
    如果状态是成功，调用成功回调函数
    如果状态是失败，调用失败回调函数

```js
const promise=new Promise((resolve,reject)=>{
    resolve('success')
    reject('err')
})
promise.then(value=>{
    console.log('resolve',value)
},reason=>{
    console.log('reject',reason)
})
```

##### Promise.all(), .race(), .allSettled()对比
all 将多个 Promise 实例，包装成一个新的 Promise 实例
```js
const p = Promise.all([p1, p2, p3]);
```
    只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，
    此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数
    只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected
    此时第一个被reject的实例的返回值，会传递给p的回调函数

race将多个 Promise 实例，包装成一个新的 Promise 实例(一个最快的调用
```js
const p = Promise.race([p1, p2, p3]);
```

   只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数
allSettled接受一个数组作为参数，数组的每个成员都是一个 Promise 对象，并返回一个新的 Promise (对象全部状态变化后才执行

    它的回调函数接收到的参数是数组results。
    该数组的每个成员都是一个对象，
    对应传入Promise.allSettled()的数组里面的Promise 对象

##### Promise、Generator、Async三者的区别
Promise的写法让代码里全是then和catch，不容易看出操作的语义
Generator语义方面比Promise更清晰，但它要求一个任务运行器，而且要求yield语句后面的表达式必须返回一个Promise
Async的函数实现是最简洁的，也是最符合语义的，相比Generator语句不用自己提供一个执行器,不需要手动调用next()就能自动执行下一步

##### async 和 await 原理
Async是Generator的语法糖
Async就是* await就是yield
加了一个自动迭代器
多个异步操作包装成的一个Promise对象,await就是里面then的语法糖

##### let const var 区别
var
	变量提升
	允许重复声明
	在函数中声明变量，该变量是局部的
let
	没有变量提升
	块级作用域内有效
	相同作用域内不允许重复声明
const
	只读的变量
	必须初始化
	不是变量的值不能改变，是变量指向的内存地址所保存的数据不得改动（对于对象和数据，它的地址不能改变，但里面的值可以改变

##### 箭头函数与普通函数区别
箭头函数没有this，它的this是通过作用域链查到外层作用域的this，且指向函数定义时的this而非执行时
不可以用作构造函数，不能使用new命令，否则会报错
箭头函数没有arguments对象，如果要用，使用rest参数代替
不可以使用yield命令，因此箭头函数不能用作Generator函数。
不能用call/apply/bind修改this指向，但可以通过修改外层作用域的this来间接修改
箭头函数没有prototype属性


##### 实现promise
核心逻辑实现
    新建MyPromise类,传入执行器 executor
```js
class MyPromise{
    constructor(executor){
        executor()
    }
}
```
    executor 传入 resolve 和 reject 方法
```js
class MyPromise{
    constructor(executor){
        executor(this.resolve,this.reject)
    }
    resolve=()=>{}
    reject=()=>{}
}
```    
    状态与结果的管理
```js
const PENDING="pending"
const FULFILLED="fulfilled"
const REJECTED="rejected"
class MyPromise{
    constructor(executor){
        executor(this.resolve,this.reject)
    }
    status=PENDING
    value=null
    reason=null
    resolve=(value)=>{
        if(this.status===PENDING){
            this.status=FULFILLED
            this.value=value
        }
    }
    reject=(reason)=>{
        if(this.status===PENDING){
            this.status=REJECTED
            this.reason=reason
        }
    }
}
```    
    then 的简单实现
```js
then(onFulfilled,onRejected){
    if(this.status===FULFILLED){
        onFulfilled(this.value)
    }else if(this.status===REJECTED){
        onRejected(this.reason)
    }
}
```    
    使用 module.exports 对外暴露 MyPromise 类
```js
module.exports=MyPromise
```    
小结
```js
const PENDING="pending"
const FULFILLED="fulfilled"
const REJECTED="rejected"
class MyPromise{
    constructor(executor){
        executor(this.resolve,this.reject)
    }
    status=PENDING
    value=null
    reason=null
    resolve=(value)=>{
        if(this.status===PENDING){
            this.status=FULFILLED
            this.value=value
        }
    }
    reject=(reason)=>{
        if(this.status===PENDING){
            this.status=REJECTED
            this.reason=reason
        }
    }
}
then(onFulfilled,onRejected){
    if(this.status===FULFILLED){
        onFulfilled(this.value)
    }else if(this.status===REJECTED){
        onRejected(this.reason)
    }
}
module.exports=MyPromise
```
在Promise类中加入异步逻辑
    缓存成功与失败回调
```js
onFulfilledCallback=null
onRejectedCallback=null
```
    then 方法中的 Pending 的处理
```js
then(onFulfilled,onRejected){
    if(this.status===FULFILLED){
        onFulfilled(this.value)
    }else if(this.status===REJECTED){
        onRejected(this.reason)
    }else if(this.status===PENDING){
        this.onFulfilledCallback=onFulfilled
        this.onRejectedCallback=onRejected
    }
}
```      
    resolve 与 reject 中调用回调函数
```js
resolve=(value)=>{
        if(this.status===PENDING){
            this.status=FULFILLED
            this.value=value
            this.onFulfilledCallback&&this.onFulfilledCallback(value)
        }
    }
reject=(reason)=>{
        if(this.status===PENDING){
            this.status=REJECTED
            this.reason=reason
            this.onRejectedCallback&&this.onRejectedCallback(reason)
        }
    }
```
实现then方法多次调用添加多个处理函数
    MyPromise 类中新增两个数组
```js
onFulfilledCallbacks=[]
onRejectedCallbacks=[]
```    
    回调函数存入数组中
```js
then(onFulfilled,onRejected){
    if(this.status===FULFILLED){
        onFulfilled(this.value)
    }else if(this.status===REJECTED){
        onRejected(this.reason)
    }else if(this.status===PENDING){
        this.onFulfilledCallbacks.push(onFulfilled)
        this.onRejectedCallbacks.push(onRejected)
    }
}
``` 
    循环调用成功和失败回调
```js
resolve=(value)=>{
        if(this.status===PENDING){
            this.status=FULFILLED
            this.value=value
            while(this.onFulfilledCallbacks.length){
                this.onFUlfilledCallbacks.shift()(value)
            }
           
        }
    }
reject=(reason)=>{
        if(this.status===PENDING){
            this.status=REJECTED
            this.reason=reason
            while(this.onRejectedCallbacks.length){
                this.onRejectedCallbacks.shift()(reason)
            }
        }
    }
```
实现then方法的链式调用
```js
class MyPromise{
    then(onFulfilled,onRejected){
        const promise2=new MyPromise((resolve,reject)=>{
            if(this.status===FULFILLED){
                const x=onFulfilled(this.value)
                resolvePromise(x,resolve,reject)
            }else if(this.status===REJECTED){
                onRejected(this.reason)
            }else if(this.status===PENDING){
                this.onFulfilledCallbacks.push(onFulfilled)
                this.onRejecredCallbacks.push(onRejected)
            }
        })
        return promise2
    }
}
function resolvePromise(x,resolve,reject){
    if(x instanceof MyPromise){
        x.then(resolve,reject)
    }else{
        resolve(x)
    }
}
```
then方法链式调用识别Promise是否返回自己
```js
class MyPromise{
    then(onFulfilled,onRejected){
        const promise2=new MyPromise((resolve,reject)=>{
            if(this.status===FULFILLED){
                queueMicrotask(()=>{
                     const x=onFulfilled(this.value)
                     resolvePromise(promise2,x,resolve,reject)
                }) 
            }else if(this.status===REJECTED){
                onRejected(this.reason)
            }else if(this.status===PENDING){
                this.onFulfilledCallbacks.push(onFulfilled)
                this.onRejecredCallbacks.push(onRejected)
            }
        })
        return promise2
    }
}
function resolvePromise(promise2,x,resolve,reject){
    if(promise2===x){
        return reject(new TypeError('Chaining cycle detected for promise #<Promise>'))
    }
    if(x instanceof MyPromise){
        x.then(resolve,reject)
    }else{
        resolve(x)
    }
}
```
捕获错误以及then链式调用其他状态代码补充
    捕获执行器错误
```js
constructor(executor){
    try{
        executor(this.resolve,this.reject)
    } catch(error){
        this.reject(error)
    }
        
    }
```    
    then 执行的时错误捕获
```js
then(onFulfilled,onRejected){
        const promise2=new MyPromise((resolve,reject)=>{
            if(this.status===FULFILLED){
                queueMicrotask(()=>{
                    try{
                        const x=onFulfilled(this.value)
                        resolvePromise(promise2,x,resolve,reject)
                    }catch(error){
                        reject(error)
                    }
                     
                }) 
            }else if(this.status===REJECTED){
                onRejected(this.reason)
            }else if(this.status===PENDING){
                this.onFulfilledCallbacks.push(onFulfilled)
                this.onRejecredCallbacks.push(onRejected)
            }
        })
        return promise2
    }
```
参考fulfilled状态下的处理方式，对rejected和pending状态进行改造
    增加异步状态下的链式调用
    增加回调函数执行结果的判断
    增加识别Promise是否返回自己
    增加错误捕获
```js
then(onFulfilled,onRejected){
        const promise2=new MyPromise((resolve,reject)=>{
            if(this.status===FULFILLED){
                queueMicrotask(()=>{
                    try{
                        const x=onFulfilled(this.value)
                        resolvePromise(promise2,x,resolve,reject)
                    }catch(error){
                        reject(error)
                    }
                     
                }) 
            }else if(this.status===REJECTED){
                queueMicrotask(()=>{
                    try{
                        const x= onRejected(this.reason)
                        resolvePromise(promise2,x,resolve,reject)
                    }catch(error){
                        reject(error)
                    }
                     
                }) 
               
            }else if(this.status===PENDING){
                 queueMicrotask(()=>{
                    try{
                        const x= onFulfilled(this.value)
                        resolvePromise(promise2,x,resolve,reject)
                    }catch(error){
                        reject(error)
                    }
                     
                }) 
                this.onFulfilledCallbacks.push(()=>{
                    queueMicrotask(()=>{
                    try{
                        const x= onFulfilled(this.value)
                        resolvePromise(promise2,x,resolve,reject)
                    }catch(error){
                        reject(error)
                    }
                })
                this.onRejecredCallbacks.push(()=>{
                    queueMicrotask(()=>{
                    try{
                        const x= onFulfilled(this.reason)
                        resolvePromise(promise2,x,resolve,reject)
                    }catch(error){
                        reject(error)
                    }
                }
                    
                )
            }
        })
        return promise2
    }
```
then中的参数变为可选
实现resolve与reject的静态调用
```js
// MyPromise.js

// 先定义三个常量表示状态
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

// 新建 MyPromise 类
class MyPromise {
  constructor(executor){
    // executor 是一个执行器，进入会立即执行
    // 并传入resolve和reject方法
    try {
      executor(this.resolve, this.reject)
    } catch (error) {
      this.reject(error)
    }
  }

  // 储存状态的变量，初始值是 pending
  status = PENDING;
  // 成功之后的值
  value = null;
  // 失败之后的原因
  reason = null;

  // 存储成功回调函数
  onFulfilledCallbacks = [];
  // 存储失败回调函数
  onRejectedCallbacks = [];

  // 更改成功后的状态
  resolve = (value) => {
    // 只有状态是等待，才执行状态修改
    if (this.status === PENDING) {
      // 状态修改为成功
      this.status = FULFILLED;
      // 保存成功之后的值
      this.value = value;
      // resolve里面将所有成功的回调拿出来执行
      while (this.onFulfilledCallbacks.length) {
        // Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
        this.onFulfilledCallbacks.shift()(value)
      }
    }
  }

  // 更改失败后的状态
  reject = (reason) => {
    // 只有状态是等待，才执行状态修改
    if (this.status === PENDING) {
      // 状态成功为失败
      this.status = REJECTED;
      // 保存失败后的原因
      this.reason = reason;
      // resolve里面将所有失败的回调拿出来执行
      while (this.onRejectedCallbacks.length) {
        this.onRejectedCallbacks.shift()(reason)
      }
    }
  }

  then(onFulfilled, onRejected) {
    const realOnFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
    const realOnRejected = typeof onRejected === 'function' ? onRejected : reason => {throw reason};

    // 为了链式调用这里直接创建一个 MyPromise，并在后面 return 出去
    const promise2 = new MyPromise((resolve, reject) => {
      const fulfilledMicrotask = () =>  {
        // 创建一个微任务等待 promise2 完成初始化
        queueMicrotask(() => {
          try {
            // 获取成功回调函数的执行结果
            const x = realOnFulfilled(this.value);
            // 传入 resolvePromise 集中处理
            resolvePromise(promise2, x, resolve, reject);
          } catch (error) {
            reject(error)
          } 
        })  
      }

      const rejectedMicrotask = () => { 
        // 创建一个微任务等待 promise2 完成初始化
        queueMicrotask(() => {
          try {
            // 调用失败回调，并且把原因返回
            const x = realOnRejected(this.reason);
            // 传入 resolvePromise 集中处理
            resolvePromise(promise2, x, resolve, reject);
          } catch (error) {
            reject(error)
          } 
        }) 
      }
      // 判断状态
      if (this.status === FULFILLED) {
        fulfilledMicrotask() 
      } else if (this.status === REJECTED) { 
        rejectedMicrotask()
      } else if (this.status === PENDING) {
        // 等待
        // 因为不知道后面状态的变化情况，所以将成功回调和失败回调存储起来
        // 等到执行成功失败函数的时候再传递
        this.onFulfilledCallbacks.push(fulfilledMicrotask);
        this.onRejectedCallbacks.push(rejectedMicrotask);
      }
    }) 
    
    return promise2;
  }

  // resolve 静态方法
  static resolve (parameter) {
    // 如果传入 MyPromise 就直接返回
    if (parameter instanceof MyPromise) {
      return parameter;
    }

    // 转成常规方式
    return new MyPromise(resolve =>  {
      resolve(parameter);
    });
  }

  // reject 静态方法
  static reject (reason) {
    return new MyPromise((resolve, reject) => {
      reject(reason);
    });
  }
}

function resolvePromise(promise2, x, resolve, reject) {
  // 如果相等了，说明return的是自己，抛出类型错误并返回
  if (promise2 === x) {
    return reject(new TypeError('Chaining cycle detected for promise #<Promise>'))
  }
  // 判断x是不是 MyPromise 实例对象
  if(x instanceof MyPromise) {
    // 执行 x，调用 then 方法，目的是将其状态变为 fulfilled 或者 rejected
    // x.then(value => resolve(value), reason => reject(reason))
    // 简化之后
    x.then(resolve, reject)
  } else{
    // 普通值
    resolve(x)
  }
}

module.exports = MyPromise;
```


##### 实现promise.all
```js
function doPromiseAll(promises){
    return new Promise((resolve,reject)=>{
        if(!Array.isArray(promises)){
            throw new TypeError("promises must be an array")
        }
        let result=[]
        let count=0
        promises.forEach((promise,index)=>{
            Promise.resolve(promise).then((res)=>{
                result[index]=res
                count++
                count===promise.length&&resolve(result)
            },(err)=>{
                reject(err)
            })
        })
    })
}
{
    function doPromiseAll(promise){
        if(!Array.isArray(promises)){throw new Error("Promises must be an Array")}
        return new Promise((resolve,reject)=>{
            const result=[]
            let count=0
            promise.forEach((p,index)=>{
                Promise.resolve(p).then((res)=>{
                    result[index]=res
                    count++
                    count===promises.length&&resolve(result)
                },err=>reject(err))
            })
        })
    }
}

```

##### 实现 promise.finally
```js
MyPromise.prototype.finally=function(callback){
    let P=this.constructor
    return this.then(
        value=>P.resolve(callback()).then(()=>value),
        reason=>P.resolve(callback()).then(()=>{throw reason})
    )
}


{
    myPromise.prototype.finally=function(callback){
        let P=this.constructor
        return this.then(
            value=>P.resolve(callback()).then(()=>value),
            reason=>P.resolve(callback()).then(()=>{throw reason})
        )
    }

    function promiseFinally=(callback){
        return this.then(
            value=>Promise.resolve(callback()).then(()=>value),
            reason=>Promise.resolve(callback()).then(()=>{throw reason})
        )
    }
}
```

##### 数组去重
双重for和splice 时间复杂度n²
```js
function unique(arr){
    for(let i=0;i<arr.length;i++){
        for(let j=1;j<arr.length;j++){
            if(arr[i]===arr[j]){
                arr.splice(j,1)
                j--
            }
        }
    }
    return arr
}
```
indexOf或者includes加入新数组
```js
//indexOf
function unique(arr){
    const uniqueArr=[]
    for(let i=0;i<arr.length;i++){
        if(uniqueArr.indexOf(arr[i])===-1){
            uniqueArr.push(arr[i])
        }
    }
    return uniqueArr
}
//includes
function unique(arr){
    const uniqueArr=[]
    for(let i=0;i<arr.length;i++){
        if(!uniqueArr.includes(arr[i])){
            uniqueArr.push(arr[i])
        }
    }
    return uniqueArr
}
```
sort排序，快慢指针
```js
function unique(arr){
    arr.sort((a,b)=>a-b)
    let slow=1,fast=1
    while(fast<arr.length){
        if(arr[fast]!=arr[fast-1]){
            arr[slow++]=arr[fast]
        }
        ++fast
    }
    arr.length=slow
    return arr
}
```
Set去重
```js
function unique(arr){
    const result=new Set(arr)
    return [...result]
}
```
使用哈希表存储
```js
functon unique(arr){
    const map = new Map()
    const uniqueArr=[]
    for(let i=0;i<arr.length;i++){
        if(map.has(arr[i])){
            map.set(arr[i],true)
        }else{
            map.set(arr[i],false)
            uniqueArr.push(arr[i])
        }
    }
    return uniqueArr
}
```
filter配合indexOf
```js
function unique(arr){
    return arr.filter(
        function(item,index,arr){
            return arr.indexOf(item)==index
        }
    )
}
```
reduce配合includes
```js
function unique(arr){
    return arr.reduce((acc,cur)=>{
        if(!acc.includes(cur)){
            acc.push(cur)
        }
        return acc
    },[])
}
```

##### 实现防抖函数debounce
```js
function debounce(func,wait=1000,immediate=true){
    //声明计时器和返回值
    let timeout,result
    //判断传入的第一个参数是否是函数
    if (typeof func !== 'function'){
        throw new TypeError('Expected a function')
    }
    //声明函数
    const debounced=function(){
        //承接上下文和参数
        const context=this
        const args=arguments
        //如果有计时器，重置
        if(timeout) clearTimeout(timeout)
        //如果选择立即执行
        if(immediate){
            //判断是否有计时器
            const callNow=!timeout
            //重置计时器，在wait时间后清除计时器
            timeout = setTimeout(function(){
                timeout=null
            },wait)
            //如果没有计时器，返回立即执行函数
            if(callNow) result=func.apply(context,args)
        }
        //如果不立即执行
        else{
            //设定计时器，在wait时间后执行
            timeout=setTimeout(function(){
                result=func.apply(context,args)
            },wait)
        }
        //返回函数
        return result
    }
    //取消防抖的方法
    debounced.cancel=function(){
        //清除计时器
        clearTimeout(timeout)
        timeout=null

    }
    //返回内部函数
    return debounced
    
}

{
    function debounce(fn,wait=500,immediate=true){
        let timeout,result
        //监测输入的类型
         if (typeof fn !== 'function'){throw new TypeError('Expected a function')}
         const debounced=funtion(){
                const context=this
                const args=arguments
                if(timeout){
                    clearTimeout(timeout)
                }
                //是否立即执行
                if(immediatee){
                    const callNow=!timeout
                    timeout = setTimeout(function(){timeout=null},wait)
                    if(callNow) result=func.apply(context,args)

                }else{
                    timeout=setTimeout(function(){result=fn.apply(context,args)},wait)
                }
                return result
         }
         //取消防抖
        debounced.cancel=funtion(){
            clearTimeout(timeout)
            timeout=null
        }
         return debounced
    }
}
{
    function debounce(fn,wait=500,immediate=true){
        let timeout,result
        if(typeof(fn)!=='function'){
            throw new TypeError('Expected a function')
        }
        const debounced=function(){
            const context=this
            const args=arguments
            if(timeout){
                clearTimeout(timeout)
            }
            if(immediate){
                    const CallNow=!=timeout
                    timeout=setTimeOut(function(){timeout=null},wait)
                    if(CallNow){
                        result=fn.apply(context,args)
                    }

            }else{
                 timeout=setTimeOut(function(){result=fn.apply(context,args)},wait)
            }
           
            return result
        }
        debounced.cancel=function(){
            clearTimeout(timeout)
            timeout=null
        }
        return debounced
    }
}
```

##### 实现节流函数throttle
```js
function throttle(func,wait,options={}){
    let timeout
    let previous=0
    return function(){
        let now =+new Date()
        let remain=wait-(now-previous)
        if(remain<0){
            if(previous===0&&!options.begin){
                previous=now
                return
            }
            if(timeout){
                clearTimeout(timeout)
                timeout=null
            }
            previous=now
            func.call(this,arguments)
        }else if(!timeout&&options.end){
            timeout=setTimeout(()=>{
                func.call(this,arguments)
                timeout = null
            },wait)
        }
    }
   
}
{
    function throttle(fn,wait=500,options={}){
            let timeout
            let previous=0
            return function(){
                let now= +new Date()
                let remain=wait-(now-previous)
                if(remain<0){
                    if(previous==0&&!option.begin){
                        previous=now
                        return
                    }
                    if(timeout){
                        clearTimeout(timeout)
                        timeout=null
                    }
                    previous=now
                    fn.call(this,arguments)
                }else if(!timeout&&options.end){
                    timeout=setTimeout(()=>{fn.call(this,arguments);timeout=null},wait)
                }
            }
    }
}
{
    //节流 在一定时间内只触发一次，设置options首尾禁用参数
    function throttle(fn,wait=500,option={}){
        let timeout
        let previous=0
        return function(){
            let now=+new Date()
            let remain=wait-(now-previous)
            if(remain<0){
                previous=now
                if(previous==0&&!option.begin){
                    return
                }
                if(timeout){
                    clearTimeout(timeout)
                    timeout=null
                }
               
                fn.call(this,arguments)

            }else if(!timeout&&option.end){
                timeout=setTimeout(()=>{fn.call(this,arguments);timeout=null},wait)
            }
        }
    }
}
```


##### 能不能写一个完整的深拷贝
简易版
```js
const deepClone=(target)=>{
    if(typeof target==='object'&&target!==null){
        const cloneTarget=Array.isArray(target)?[]:{}
        for(const prop in target){
            if(target.hasOwnProperty(prop)){
                cloneTarget[prop]=deepClone(target[prop])
            }
        }
        return cloneTarget
    }else{
        return target
    }
}

```
解决循环引用（整个Map，如果拷贝过，直接返回，不然无限套娃栈溢出
```js
const isObject=(target)=>(typeof target==='object'||typeof target === 'function')&&target!==null
const deepClone=(target,map=new Map())=>{
    if(map.get(target)){
        return target
    }
    if(isObject(target)){
        map.set(target,true)
        const cloneTarget=Array.isArray(target)?[]:{}
        for(let prop in target){
            if(target.hasOwnProperty(prop)){
                cloneTarget[prop]=deepClone(target[prop],map)
            }
        }
        return cloneTarget
    }else{
        return target
    }
}
```
存在的问题：map上的key和map构成了强引用关系
强引用的对象在程序结束前，内存空间不会被释放
弱引用的对象在任何时候都能被回收
解决方法：使用WeakMap
```js
const isObject=(target)=>(typeof target==='object'||typeof target === 'function')&&target!==null
const deepClone=(target,map=new WeakMap())=>{
    if(map.get(target)){
        return target
    }
    if(isObject(target)){
        map.set(target,true)
        const cloneTarget=Array.isArray(target)?[]:{}
        for(let prop in target){
            if(target.hasOwnProperty(prop)){
                cloneTarget[prop]=deepClone(target[prop],map)
            }
        }
        return cloneTarget
    }else{
        return target
    }
}

{
    const isObject=(target)=>(typeof target==='object'||typeof target==='function')&&target!=null
    const deepClone=(target,map=new WeakMap())=>{
        if(map.get(target)){
            return target
        }
        if(ifObject(target)){
            map.set(target,true)
            const cloneTarget=Array.isArray(target)?[]:{}
            for(const prop in target){
                if(target.hasOwnProperty(prop)){
                    cloneTarget[prop]=deepClone(target[prop],map)
                }
            }
            return cloneTarget
        }else{
            return target
        }
    }
}
```



### 中频问题（20个）
##### 垃圾回收机制
Garbage Collection，释放程序不用的内存，Python、JS、JAVA自带，C、C++需要手动管理内存
JavaScript内存管理中有个概念叫做 可达性
    那些以某种方式可访问或者说可用的值
    它们被保证存储在内存中
    反之不可访问则需回收
JavaScript 垃圾回收机制的原理说白了也就是
    定期找出那些不再用到的内存（变量），然后释放其内存
标记清除算法
    标记清除（Mark-Sweep）目前在 JavaScript引擎 里这种算法是最常用的
    到目前为止的大多数浏览器的 JavaScript引擎 都在采用标记清除算法
    只是各大浏览器厂商还对此算法进行了优化加工
    且不同浏览器的 JavaScript引擎 在运行垃圾回收的频率上有所差异
    实现简单,但有内存碎片化和分配速度慢的问题

    标记整理（Mark-Compact）算法
    标记结束后，标记整理算法会将活着的对象（即不需要清理的对象）向内存的一端移动，最后清理掉边界的内存

引用计数算法
    引用计数（Reference Counting）
    这其实是早先的一种垃圾回收算法，
    它把 对象是否不再需要 简化定义为 对象有没有其他对象引用到它
    如果没有引用指向该对象（零引用），对象将被垃圾回收机制回收，
    目前很少使用这种算法了，因为它的问题很多
    循环引用问题

##### 继承的多种方式和优缺点
原型链继承
    引用类型的属性被所有实例共享
    在创建 Child 的实例时，不能向Parent传参
借用构造函数(经典继承)
```js
function Child(){
    Parent.call(this,name)
}
```    
    避免了引用类型的属性被所有实例共享
    可以在 Child 中向 Parent 传参
    但是方法都在构造函数中定义，每次创建实例都会创建一遍方法
组合继承
    原型链继承和经典继承双剑合璧
    融合原型链继承和构造函数的优点，是 JavaScript 中最常用的继承模式
    组合继承最大的缺点是会调用两次父构造函数
    一次是设置子类型实例的原型的时候
    Child.prototype = new Parent();
    一次在创建子类型实例的时候
    var child1 = new Child('kevin', '18');
原型式继承
```js
function createObj(o) {
    function F(){}
    F.prototype = o;
    return new F();
}
```
寄生式继承
    创建一个仅用于封装继承过程的函数，
    该函数在内部以某种形式来做增强对象，最后返回对象
```js
function createObj (o) {
    var clone = object.create(o);
    clone.sayName = function () {
        console.log('hi');
    }
    return clone;
}
```
    缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法
寄生组合式继承 
    只调用了一次 Parent 构造函数
    并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性
    与此同时，原型链还能保持不变
    还能够正常使用 instanceof 和 isPrototypeOf

##### JS原型链继承与Class继承
基于原型继承
    原型实现继承的核心在于通过子类的构造函数中通过Parent.call(this)继承父类的属性，然后改变子类的原型为new Parent() 来继承父类的函数
基于Class继承
    extends和super
```js
//ES6 extends 继承父类
class Student extends People {
    constructor(name = 'student1', age = '22', score = 90) {
        //继承父类属性
        super(name, age);
        //自身属性
        this.score = score;
    }
    sayHi() {
        //继承父类属性方法
        super.sayHi()
        //自身方法
        console.log('score：' + this.score)
    }
}
```

##### call、apply、bind区别
call() 和apply()的第一个参数相同，就是指定的对象。这个对象就是该函数的执行上下文
call()和apply()的区别就在于，两者之间的参数
call()在第一个参数之后的 后续所有参数就是传入该函数的值
apply() 只有两个参数，第一个是对象，第二个是数组，这个数组就是该函数的参数

bind() 方法和前两者不同在于： 
    bind() 方法会返回执行上下文被改变的函数而不会立即执行，
    而前两者是 直接执行该函数。
    他的参数和call()相同。

##### cookie, localStorage sessionStorage区别
cookie
    可设置失效时间，没有设置的话，默认是关闭浏览器后失效
    4K左右
    每次都会携带在HTTP头中，如果使用cookie保存过多数据会带来性能问题
    需要程序员自己封装，源生的Cookie接口不友好
localStorage
    除非被清除，否则永久保存
    一般为5MB
    仅在客户端（即浏览器）中保存，不参与和服务器的通信
    源生接口可以接受，亦可再次封装来对Object和Array有更好的支持
sessionStorage
    仅在当前会话下有效，关闭页面或浏览器后被清除
    一般为5MB
    仅在客户端（即浏览器）中保存，不参与和服务器的通信
    源生接口可以接受，亦可再次封装来对Object和Array有更好的支持


##### 数组高阶方法
forEach
map     对数组的每个元素执行回调函数，并返回含有运算结果的新数组
filter  它对数组每个元素执行回调函数，返回回调函数执行结果为true的元素
every   数组的每个元素执行回调函数，如果执行结果全为 true ，every 返回 true，否则返回 false
some    只要回调函数结果有一个 true，some便返回 true，否则返回 false
find/findIndex  返回满足回调函数的第一个数组元素/数组元素索引。当数组中没有能满足回调函数的元素时，会分别返回 undefined和-1
reduce  

##### 防抖与节流的区别
防抖 (debounce): 将多次高频操作优化为只在最后一次执行，
    通常使用的场景是：用户输入，只需再输入完成后做一次输入校验即可
节流(throttle): 每隔一段时间后执行一次，也就是降低频率，
    将高频操作优化成低频操作，通常使用场景: 
    滚动条事件 或者 resize 事件，通常每隔 100~500 ms执行一次即可。


##### defineProperty与proxy区别
definePropety
    在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回这个对象
    Object.defineProperty(obj, prop, descriptor)
    通过setter监控对象属性的改变
Proxy
    可以重定义更多的行为，比如 in、delete、函数调用等更多行为

当使用 defineProperty，我们修改原来的 obj 对象就可以触发拦截
而使用 proxy，就必须修改代理对象，即 Proxy 的实例才可以触发拦截


##### map与weakMap的区别
WeakMap 结构与 Map 结构类似，也是用于生成键值对的集合
    只接受对象作为键名（null 除外），不接受其他类型的值作为键名
    键名是弱引用，键值可以是任意的，键名所指向的对象可以被垃圾回收，此时键名是无效的
    不能遍历，方法有 get、set、has、delete


##### set 与 weakSet 区别
WeakSet 结构与 Set 类似，也是不重复的值的集合。
    WeakSet 成员都是数组和类似数组的对象，若调用 add() 方法时传入了非数组和类似数组的对象的参数，就会抛出错误
    WeakSet 成员都是弱引用，可以被垃圾回收机制回收，可以用来保存 DOM 节点，不容易造成内存泄漏
    WeakSet 不可迭代，因此不能被用在 for-of 等循环中
    WeakSet 没有 size 属性


##### map与object区别
初始化
    map 只能通过 new 关键字和构造函数创建
    object 可以使用字面量、构造函数、Object.crate的形式创建
键值
    object键值只能使用数组、字符串、符号作为键
    map 的键值可以是任意类型
顺序与迭代
    object的键值 key 的遍历顺序
        首先遍历所有数值键，按照数值升序排列
        其次遍历所有字符串键，按照加入时间升序排列
        后遍历所有 Symbol 键，按照加入时间升序排列
    map 会维护键值对的插入顺序，因此遍历顺序就是插入顺序

##### map与set
 - Map对象保存键值对。任何值(对象或者原始值) 都可以作为一个键或一个值。
    构造函数Map可以接受一个数组作为参数。
 - Set对象允许你存储任何类型的值，无论是原始值或者是对象引用。
    它类似于数组，但是成员的值都是唯一的，没有重复的值。
    Set 本身是一个构造函数，用来生成Set 数据结构。
    Set函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。
 - map存键值对，set存一个个值或者对象引用
 - map有get,set;set有add。都有has\delete\clear\keys\values\entries\forEach


##### 如何在使用async & await 时优雅的处理异常
基础的方法是try...catch模块
优雅的解决方法
    制作一个通用函数接收一个Promise，
    然后将处理成功的返回值以数组的形式作为附加值返回，
    并且在catch方法中接收到捕捉到的第一个错误
```js
// to.js
export default function to(promise) {
   return promise.then(data => {
      return [null, data];
   })
   .catch(err => [err]);
}

```
```js
import to from './to.js';
async function asyncTask(cb) {
     let err, user, savedTask;
     [err, user] = await to(UserModel.findById(1));
     if(!user) return cb('No user found');
     [err, savedTask] = await to(TaskModel({userId: user.id, name: 'Demo Task'}));
     if(err) return cb('Error occurred while saving task');

    if(user.notificationsEnabled) {
       const [err] = await to(NotificationService.sendNotification(user.id, 'Task Created'));  
       if(err) return cb('Error while sending notification');
    }

    cb(null, savedTask);
}

```
##### 实现promise.allSettled
//在全部Promise执行完毕后再继续
```js
function allSettled(promises){
    if(promises.length===0){
        return Promise.resolve([])
    }
    const _promises=promises.map(item=>Promise.resolve(item))
    return new Promise((resolve,reject)=>{
        const result=[]
        let unSettledPromiseCount=_promises.length
        _promises.forEach((promise,index)=>{
            promise.then((value)=>{
                result[index]={
                    status:'fulfiled',
                    value
                }
                unSettledPromiseCount-=1
                if(unSettledPromiseCount===0){
                    resolve(result)
                }
            },(reson)=>{
                result[index]={
                    status:'rejected',
                    reson
                }
                unSettledPromiseCount-=1
                if(unSettledPromiseCount===0){
                    resolve(result)
                }
            })
        })
    })
}

{
    function allSettled(promises){
        if(promises.length==0){
            return Promise.resolve([])
        }
        const _promises=promises.map(item=>Promise.resolve(item))
        return new Promise((resolve,reject)=>{
            const result=[]
            let count=0
            _promises.forEach((p,index)=>{
                p.then((value)=>{
                    result[index]={
                        sataus:'fulfiled',
                        value
                    }
                    count++
                    count==_promises.length&&resolve(result)
                },(reson){
                    result[index]={
                        sataus:'rejected',
                        reson
                    }
                    count++
                    count==_promises.length&&resolve(result)
                })
            })
        })
    }
}
{
    function allSettled(promises){
        if(promises.length==0){
            return Promise.resolve([])
        }
        const _promises=promises.map((item)=>Promise.resolve(item))
        return new Promise((resolve,reject)=>{
            const result=[]
            let count=0
            _promises.forEach((p,index)=>{
                p.then((value)=>{
                    result[index]={
                        status:"fulfiled",
                        value
                    }
                    count++
                    count===_promises.length&&resolve(result)
                },(reason)=>{
                    result[index]={
                        status:"rejected",
                        reason
                    }
                    count++
                    count===_promises.length&&resolve(result)
                })
            })

        })
    }
}
```
##### Object 实现new
```js
function createObject(Con){
    //创建新对象
    var obj={}
    //将该对象的原型指向构造函数的原型
    Object.setPrototypeOf(obj,Con.prototype)
    //执行构造函数
    const ret=Con.apply(obj,[].slice.call(arguments,1))
    return typeof(ret)==='object'?ret:obj
}
```
##### Object继承
    看上面关于继承的总结
    
##### call
```js
//使用eval方法
Function.prototype.myCall=function(thisArg){
    thisArg=thisArg||window
    thisArg.func=this
    const args=[]
    for(let i=1;i<arguments.length;i++){
        args.push['arguments['+i+']']
    }
    const result=eval('thisArg.func('+args+')')
    delete thisArg.func
    return result
}

```
##### bind
```js
Function.prototype.myBind=function(obj,...args){
    obj=obj||window
    const fn=Symbol()
    obj[fn]=this
    const _this=this
    const res=function(...innerArgs){
        console.log(this,_this)
        if(this instanceof _this){
            this[fn]=_this
            this[fn](...[...args,...innerArgs])
            delete this[fn]
        }else{
            obj[fn](...[...args,...innerArgs])
            delete obj[fn]
        }
    }
    res.prototype=Object.creat(this.prototype)
    return res
}
```

##### apply
```js
Function.prototype.myApply=function(thisArg,arr){
    thisArg=thisArg||window
    thisArg.func=this
    const args=[]
    for(let i=1;i<arr.length;i++){
        args.push('arr['+i+']')
    }
    const result=eval('thisArg.func('+args+')')
    delete thisArg.func
    return result
}
```
##### instanceof
```js
function instance_of(Case,Constructor){
    //基本数据类型返回false
    //兼容函数对象
    if((typeof(Case)!='object'&&typeof(Case)!='function')||Case=='null'){
        return false
    }
    let CaseProto=Object.getPrototypeOf(Case)
    while(true){
        //一直到顶
        if(CaseProto==null){
            return false
        }
        //找到一样的原型
        if(CaseProto==Constructor.prototype){
            return true
        }
        CaseProto=Object.getPrototypeOf(CaseProto)
    }
}

{
    function instance_of(Case,Constructor){
        if((typeof(Case)!='object'&&typeof(Case)!='function')||Case=='null'){
            return false
        }
        let CaseProto=Object.getPrototypeOf(Case)
        while(true){
            if(CaseProto==null){
                return false
            }
            if(CaseProto==Constructor.prototype){
                return true
            }
            CaseProto=Object.getPrototypeOf(CaseProto)
        }
    }
}
```
### 其他问题
##### Promise.race
```js
Promise.race = function(promiseArr) {
    return new Promise((resolve, reject) => {
        promiseArr.forEach(p => {
            Promise.resolve(p).then(val => {
                resolve(val)
            }, err => {
                rejecte(err)
            })
        })
    })
}

{
    function race(promises=[]){
        return new Promise((resolve,reject)=>{
            promises.forEach(p=>{
                Promise.resolve(p).then(val=>{
                    resolve(val)
                },err=>{
                    reject(err)
                })
            })
        })
    }
}
```
##### 柯里化
- 参数复用
```js
function curry(fn,args){
    const n=fn.length
    args=args||[]
    return function(){
        const _args=args.slice(0)
        for(let i=0;i<arguments.length;i++){
            const arg=arguments[i]
            _args.push(arg)
        }
        if(_args.length<n){
            return curry.call(this,fn,_args)
        }
        else{
            return fn.apply(this,_args)
        }
    }

}
const fn=curry(function(a,b,c){
    console.log([a,b,c])
})
fn("a")("b")("c")

//手写练习
//写一个柯里化函数
function curry(fn,args=[]){
    //函数的参数
    const n=fn.length
    return function(){
        const _args=args.slice(0)
        for(let i=0;i<arguments.length;i++){
            _args.push(arguments[i])
        }
        if(_args.length<n){
            return curry.call(this,fn,_args)
        }else{
            return fn.apply(this,_args)
        }
    }
}
//第二遍,fn参数固定
function curry(fn,args=[]){
        const n=fn.length
        return function(){
            const _args=args.slice(0)
            for(const a of arguments){
                _args.push(a)
            }
            if(_args.length<n){
                return curry.call(this,fn,_args)
            }else{
                return fn.apply(this,_args)
            }
        }
}




```
```js
//经典累加面试题
function add(){
        //先把所有参数存起来
    const _args=Array.prototype.slice.call(arguments)
    //闭包，保存_args
    const _adder=function(){
        _args.push(...arguments)
        return _adder
    }
    //重写toString
    _adder.toString=function(){
        return _args.reduce(function(a,b){
            return a+b
        })
    }
    return _adder
    
}

{
function add(){
    const _args=[...arguments]
    const _adder=function(){
        _args.push(...arguments)
        return _adder
    }
    _adder.toString=function(){
        return _args.reduce(function(a,b){
            return a+b
        })
    }
    return _adder
}
add(1)(2)(3).toString()
}
//第二遍
function add(){
    const _args=[...arguments]
    const adder=function(){
        _args.push(...arguments)
        return adder
    }
    adder.toString=function(){
        return _args.reduce(function(a,b){return a+b})
    }
    return adder
}
add(1,2)(3).toString()


```

##### 设计模式
- 单例模式
    保证一个类只有一个实例，例如同一种类型的弹窗应该只被创建一次
- 策略模式
    定义一系列的方法，把它们封装起来，并且可以互相替换
```js
/*策略类*/
var levelOBJ = {
    "A": function(money) {
        return money * 4;
    },
    "B" : function(money) {
        return money * 3;
    },
    "C" : function(money) {
        return money * 2;
    } 
};
/*环境类*/
var calculateBouns =function(level,money) {
    return levelOBJ[level](money);
};
console.log(calculateBouns('A',10000)); // 40000
```
- 代理模式
    为一个对象提供一个代用品或者占位符，以便控制对他的访问
    比如图片懒加载，用一张很小的预加载图片放上去，当异步加载好或者图片将要出现在屏幕上再把真正要展示的图片src替换他
- 中介者模式
    通过中介者对象，其他相关对象通过它来通信，而不是相互引用，解除对象与对象之间的紧耦合
    例如购物车的更新，商品型号、数量的变化，通过中介者转发处理
- 装饰者模式
    在不改变对象自身的基础上，在程序运行期间给对象动态地添加方法
    原有方法维持不变，在原有方法上再挂载其他方法来满足现有需求
    函数的解耦，将函数拆分成多个可复用的函数，再将拆分出来的函数挂载到某个函数上，实现相同的效果但增强了复用性。

##### 写一个自定义事件类型，包含on/off/once/emit
```js
function Event(){
    this._events={}
}
//添加监听
Event.prototype.on=function(type,fn){
    if(!this._events[type]){
        this._events[type]=[]
    }
    this._events[type].push(fn)
}
//移除监听
Event.prototype.off=function(type,fn){
    if(!this._events[type]){
        return
    }
    if(!fn){
        this._events[type]=undefined
        return
    }
    const index=this._events[type].indexOf(fn)
    this._events[type].splice(index,1)
}
//提交执行
Event.prototype.emit=function(type){
    if(!this_events[type]){
        return
    }
    //把该事件的所有监听函数都执行
    this._events[type].forEach(fn=>fn())
}
//只执行一次的监听
Event.prototype.once=function(type,fn){
    const _=this
    const _fn=()=>{
        fn.apply(_,arguments)
        //执行完移除
        this.off(type)
    }
    this.on(type,_fn)
}

{
    class EventEmitter {
    constructor() {
        this.cache = {}
    }
    on(name, fn) {
        if (this.cache[name]) {
            this.cache[name].push(fn)
        } else {
            this.cache[name] = [fn]
        }
    }
    off(name, fn) {
        let tasks = this.cache[name]
        if (tasks) {
            const index = tasks.findIndex(f => f === fn || f.callback === fn)
            if (index >= 0) {
                tasks.splice(index, 1)
            }
        }
    }
    emit(name, once = false, ...args) {
        if (this.cache[name]) {
            // 创建副本，如果回调函数内继续注册相同事件，会造成死循环
            let tasks = this.cache[name].slice()
            for (let fn of tasks) {
                fn(...args)
            }
            if (once) {
                delete this.cache[name]
            }
        }
    }
}
}
```
装饰者模式、观察者模式？

##### 模拟可选链操作符 ?.
```js
function fn(obj={},keys=[]){
    return keys.reduce((pre,cur)=>{
        let ans=undefined
        if(pre.hasOwnProperty(cur)){
            ans=pre[cur]
        }
        return ans
    },obj)  
}

const A={
    a:{b:"AB"},
    b:"B",
    c:"C"
}
fn(A,["a","b","c"])

```