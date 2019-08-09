---
title: 大规模NodeJS项目架构与优化
date: 2019-06-18 16:49:52
tags: ['nodejs','架构']
categories: nodejs
---
# 大规模NodeJS项目架构与优化

## 前端使用NodeJS的意义与优势

1. proxy代理跨越，自给自足提供api,前端控制路由，前后端完全分离
2. vue的同构化，实现SSR服务端渲染，将组件拼接成html,返回给用户,实现性能优化  
3. 做了proxy代理，可以削减api
4. Nodejs <==> java 之间的通信很快 100ms以内搞定 一般10ms,不用使用http,http是昂贵和缓慢的
5. Nodejs实现跨端 实现PC、物联网、涉及到系统的层次，浏览器是无法实现的
6. 做游戏的中间层，因为NodeJS支持大量的吞吐并发，而且占用的服务器性能少，节省企业成本

## 前端开发的几种模式
![](/assets/前端开发的几种模式.jpg)
1. SPA模式（CSR）： 前端使用vue-cli,本质是产生一个index.html，使用假路由（history/hash）页面跳转,CSR渲染模式，会出现跨域问题，使用代理proxy（nginx/nodejs）处理，使用nodejs的时候，例如后端框架koa会产生一个真路由，这样的话，假如前端路由访问a/b,后端也有个路由a/b,这样页面就会返回真路由数据，显示一段json,这样是不行的。可以通过koa2-connect-history-api-fallback,让页面不管怎样都跳转到index.html，设置白名单，例如api/a/b解决真假路由问题。
2. MPA模式(SSR)：nodejs+swig+vuejs，这样的话就相当于把Vuejs当JQ用，Swig后端的模板引擎
3. 纯MPA模式（SSR）: swig+nodejs
4. 同构化（以后详细再唠）

## 异步IO原理浅析

### 异步IO的好处
1. 前端通过异步IO可以消除UI阻塞
2. 假设请求资源A的时间为M,请求资源B的时间为N,那么同步请求时间为M+N，如果是异步方式占用时间就是Max(M,N)
3. 随着业务的复杂，会引入分布式系统，时间会线性的增加，M+N+...和Max(M,N)，这会放大同步和异步之间的差异
4. I/O是昂贵的，分布式I/O是更昂贵的。 
    - I/O的一些基础知识
      - I/O的类型
       ![](/assets/IO类型.png)
       CPU时钟周期:1/cpu主频 => 1s/3.1 GHz
      - 文件操作符：系统操作文件是通过文件操作符的，而文件操作符是有限的
       ![](/assets/文件操作符.png)
5. NodeJS适用于IO密集型不适合用CPU密集型
   - IO密集型： 需要大量的文件读写，一个网站很多人访问
   - CPU密集型： 需要精密准确的计算，一个人访问网站，但是需要大量的计算，接近底层语言适合
   - Node对异步IO的实现
      ![](/assets/Node对异步IO的实现.png)
### 几个特殊的API

 1. setTimeout和setInterval线程池不参与 Event loop，LIBUV 不管这些。
 2. Process.nextTick() 实现类似 setTimeout(function(){},0);当前不执行，放入队列中，在下一轮循环中取出。
 3. setImmediate()比Process.nextTick()优先级高
> **同步 > (异步队列优先) > nextTick > Promise > setTimeout(时间) > setImmediate**
```js
    setTimeout(function () {
        console.log(1);
    }, 0);

    setImmediate(function () {
        console.log(2);
    });

    process.nextTick(() => {
        console.log(3);
    });

    new Promise((resovle,reject)=>{
        console.log(4);
        resovle(4);
    }).then(function(){
        console.log(5);
    });
    console.log(6);
   
   //执行结果： 4 6  3 5 1 2  
```
    - //  4 声明的Promise是同步的  then是异步的
    - //  6 同步队列
    - //  3 process.nextTick() 是 Node 的一个定时器，让任务可以在指定的时间运行。其中 Node 一共提供了4个定时器，它们分别是 setTimeout()、setInterval()、setImmediate()、process.nextTick()。process.nextTick它是在本轮循环执行的，而且是所有异步任务里面最快执行的。
    - setTimeout和setImmediate是哥俩，这俩优先级得看时间，当setTimeout的时间是0的时候 setTimeout>setTimmediate
  4. Sleep函数的实现
    ```js
        async function test() {
            console.log('Hello')
            await sleep(1000)
            console.log('world!')
        }
        function sleep(ms) {
            return new Promise(resolve => setTimeout(resolve, ms))
        }
        test()
    ```
## 内存管理与优化

### v8 垃圾回收机制

V8的垃圾回收策略主要基于分代式垃圾回收机制。在自动垃圾回收的演变过程中,人们发现没有一种垃圾回收算法能够胜任所有场景。V8中内存分为新生代和老生代两代。新生代为存活时间较短对象,老生代中为存活时间较长的对象。

#### Scavenge算法

- Scavenge管理新生代内存 

> 在分代基础上,新生代的对象主要通过Scavenge算法进行垃圾回收,再具体实现时主要采用Cheney算法。Cheney算
法是一种采用复制的方式实现的垃圾回收算法。它将内存一分为二,每一个空间称为semispace。这两个semispace中
一个处于使用,一个处于闲置。处于使用的称之为From,闲置的称之为To.分配对象时先分配到From,当开始进行垃圾回收时,检查From存活对象赋值到To.非存活被释放。然后互换位置。再次进行回收,发现被回收过直接晋升,或者发现To空间已经使用了超过25%。他的缺点是只能使用堆内存的一半,这是一个典型的空间换时间的办法,但是新生代声明周期较短,恰恰就适合这个算法。

新生代是等到 from 沾满了 就进行GC,
![](/assets/scavenge算法.png)

#### 老生代如何管理内存的

> V8老生代主要采用Mark-Sweep和Mark-compact,在使用Scavenge不合适。一个是对象较多需要赋值量太大而且还是没能解决空间问题。Mark-Sweep是标记清楚,标记那些死亡的对象,然后清除。但是清除过后出现内存不连续的情况,所以我们要使用Mark-compact,他是基于Mark-Sweep演变而来的,他先将活着的对象移到一边,移动完成后,直接清理边界外的内存。当CPU空间不足的时候会非常的高效。V8后续还引入了延迟处理,增量处理,并计划引入并行标记处理。

老生代采用标记清除，间歇性，不定期的GC
![](/assets/mark_sweep&mark_compact.png)

- 老生代64位系统下约为1.4GB,32位操作系统下是0.7G,  新生代64位是32M 32位是16M
- Process.memoryUsage
  - rss :所有内存使用包括堆区和栈区
  - heaptTotal:堆区占用内存
  - heapUsed:已使用到的堆部分
  - external: V8引擎C++对象占用(GC动态变化)

### 常见内存泄露问题

1. 无限制增长的数组
2. 无线设置属性和值
3. 任何模块内的私有变量和方法均是永驻内存的 a = null
4. 大循环，无GC机会

## Nodejs 集群的应用

本机启动负载均衡使用PM2,网络启动负载均衡使用nginx

### PM2
pm2 是一个带有负载均衡功能的Node应用的进程管理器.当你要把你的独立代码利用全部的服务器上的所有CPU,并保证进程永远都活着,0秒的重载。
1. 内建负载均衡(使用Node cluster 集群模块)
2. 后台运行
3. 0秒停机重载
4. 具有Ubuntu和CentOS 的启动脚本
5. 停止不稳定的进程(避免无限循环)
6. 控制台检测
7. 提供 HTTP API
8. 远程控制和实时的接口API ( Nodejs 模块,允许和PM2进程管理器交互 )

前端集群需要会配置: nginx cdn pm2

