---
title: JavaScript Promise base
date: 2019-07-05 11:16:59
tags:
description: promise基础
---
### 1、Promise是什么？
* promsie是ES6新增的一个特性，它已经列入ES6的正式规范中
* promise是抽象处理对象以及对其进行各种操作的组件，是一种异步流程的控制手段。
* javaScript和Node都是单线程非阻塞的，所以就会有异步回调的问题，promise就是为了解决这类问题的。
* Promise可以支持多个并发的请求，获取并发请求中的数据。
* Promise可以使用链式调用的方法来组织代码，所以使用Promise对象后，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套让代码更加的直观。
<!--more-->
###### 示例：
```javascript
$.ajax(url1, function(data1){
    // do something...
    $.ajax(url2, function(data2){
        // do something...
        $.ajax(url3, function(data3){
            // do something...
            done(data3); // 返回数据
        });
    });
});
```
> 未使用promise，回调必须层层嵌套(回调地狱)，代码难以维护，第一的输出是第二个的输入时，需要等待上一个操作完成才可以进行下一个操作，造成不必要的等待。
---
### 2、Promise的状态
##### 2.1 Promise对象有三种状态
- **Pending（等待态）**
  + 既不是resolve也不是reject的状态，也就是promise对象刚创建后的初始化状态；
- **Resolved/Fulfilled（成功态）**
  + resolve（成功）时，此时会调用onFuifilled;
- **Rejected（失败态）**
  + reject（失败）时，此时会调用onRejected;
> promise只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。
##### 2.2 状态一旦改变就不会再变，任何时候都可以得到这个结果。
- **如果一旦promise成功了就不能失败，相反也是一样的，只有状态是等待的状态时才可以转化状态**
  + promise对象的状态改变，只有两种可能：从Pending变为Resolved和从Pending变为Rejected
> pending->fulfilledpending->rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果.
##### 2.3 Promise的缺点
* 无法取消，一旦新建就会立即执行，无法中途取消。
* 如果不设置回调函数，promise内部抛出的错误，不会反应到外部。
* 当处于pending状态时，无法确定进展到哪一阶段，刚开始还是即将完成。
---
### 3、Promise的使用
##### 3.1基本用法
* 首先要new一个Promise，将Promise实例化
- 实例化的promise可以传两个参数，一个是成功之后的resolve，一个是失败之后的reject
  + Promise本省只有一个参数，叫executor（执行器），默认new时就会调用
- 每一个Promise的实例上都有一个then方法(异步的)，可以用then方法分别指定Resolved状态和Reject状态的回调函数
> **代码示例:** 默认promise中的executor默认执行
```javascript
let p=new Promise((resolve,reject)=>{
    console.log(1);
});
console.log(2);
```
###### 
![执行顺序](https://user-gold-cdn.xitu.io/2018/6/25/164360e6396242ae?w=720&h=232&f=png&s=14297)
> **代码示例:** 调用onFuifilled
```javascript
let p=new Promise((resolve,reject)=>{
  resolve('成功');
});

p.then((value)=>{//value成功的原因
  console.log(value);
},(err)=>{//err失败的原因
  console.log(err);
})
```
![成功](https://user-gold-cdn.xitu.io/2018/6/25/164360e64f1ddac5?w=720&h=232&f=png&s=20296)
> **代码示例:** 调用onRejected
```javascript
let p=new Promise((resolve,reject)=>{
  reject('失败');
});

p.then((value)=>{//value成功的原因
  console.log(value);
},(err)=>{//err失败的原因
  console.log(err);
})
```
![失败](https://user-gold-cdn.xitu.io/2018/6/25/164360e6aad4da4c?w=720&h=238&f=png&s=20396)
> **代码示例:** 成功了就不能失败，相反也一样
```javascript
let p=new Promise((resolve,reject)=>{//
  resolve('成功');
  reject('失败');
});

p.then((value)=>{//value成功的原因
  console.log(value);
},(err)=>{//err失败的原因
  console.log(err);
})
```
![成功就不能失败](https://user-gold-cdn.xitu.io/2018/6/25/164360e55092c8a2?w=720&h=258&f=png&s=20542)
> **代码示例:** 成功了就不能失败，相反也一样
```javascript
let p=new Promise((resolve,reject)=>{
    throw new Error('发生错误');
});
p.then((value)=>{//value成功的原因
	console.log(value);
},(err)=>{//err失败的原因
	console.log(err);
})
```
![发生错误](https://user-gold-cdn.xitu.io/2018/6/25/164360e54eff0880?w=720&h=276&f=png&s=23449)
##### 3.2链式操作
* **通过维护状态、传递状态的方式来使回调能够及时的调用，上一个操作完成之后才可以进行下一个操作，相比callback更灵活简单**
* **promise实现链式调用返回的并不是this，而是一个新的promise**
> **代码示例：**
```javascript
Promise1().then((value)=>{
  console.log(valuse);
  return Promise2();
}).then((value)=>{
  console.log(value);
  return Promise3();
}).then((value)=>{
  console.log(value);
})//可以then到天荒地老

function Promise1(){
  return new Promsie((resolve,reject)=>{
    setTimeout(function(){//异步操作
      console.log('异步1');
      resolve('异步1传的值');
    },1000);
  })
}
function Promise2(){
  return new Promsie((resolve,reject)=>{
    setTimeout(function(){//异步操作
      console.log('异步2');
      resolve('异步2传的值');
    },3000);
  })
}
function Promise3(){
  return new Promsie((resolve,reject)=>{
    setTimeout(function(){//异步操作
      console.log('异步3');
      resolve('异步3传的值');
    },2000);
  })
}
```
> **输出的值为：**
  异步1
  异步1传的值
  异步2
  异步2传的值
  异步3
  异步3传的值
##### 3.2 Promise的catch方法
* **catch方法是then(onFulfilled,onRejected)方法中onRejected函数的简单写法，也就是可以写成then(fn).catch(fn)，相当于then(fn).then(null,fn)；**
> **代码示例：**
```javascript
let p=((isReady)=>{
  return new Promise(()=>{
    if(isready){
      return resolve('hello');
    }else{
      return reject('failure');
    }
  });
})

p(true).then((value)=>{
    console.log('resolved');
    console.log(value);
    console.log(aa); 
  }).catch((err)=>{
    console.log('rejected');
    console.log(err);
  });
```
> **输出的值为：**
  resolved
  hell
  rejected
  ReferenceError: aa is not defined...
##### 3.3 promise.all方法
* **promise.all接收一个为promise对象的数组作为参数，当这个数组里所有的promise对象都变为resolve时，该方法才会返回。**
> **代码示例：**
```javascript
let p1=new Promise((resolve,reject)=>{
  setTimeout(function(){
    resolve('Promise1');
  },1000);
})
let p2=new Promise((resolve,reject)=>{
  setTimeout(function(){
    resolve('Promise2');
  },3000);
})

Promise.all([p1,p2]).then((result)=>{
  console.log(result);
})
```
> **输出结果**
  ['Promise1','Promise2']
##### 3.4 promise.race方法
* **race的意思赛跑，也就是看谁跑的快，跑的快的就赢了。因此，promise.race也是传入一个数组，但是与promise.all不同的是，promise.race只返回跑的快的值。**
> **代码实例：**
```javascript
  let p1=new Promise((resolve,reject)=>{
    setTimeout(function(){
      console.log(1);
      resolve('Promise');
    },3000);
  })
  let p2=new Promise((resolve,reject)=>{
    console.log(2);
    resolve('Promise2');
  },1000)

  Promise.race([p1,p2]).then((result)=>{
    console.log(result);
  })
````
> **输出结果：**
  2
  Promised
  1
* 可以看到传的值中，只有p2的返回了，但p1没有停止，依然执行。
---
#### <p style="color:#5a5a5a; text-align:center;">Promise的基础就到这里了，如果您觉得文章有用请点赞！！！</p>
