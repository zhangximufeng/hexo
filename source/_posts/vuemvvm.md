---
title: 剖析Vue原理&实现双向绑定MVVM
categories: 前端
tags: 
    - 技术
    - vue.js
    - mvvm
    - 双向绑定
    - 源码解析
    - javascript
    - 前端
---
> 本文能帮你做什么？  
> 1.了解vue的双向数据绑定原理以及核心代码模块  
> 2.缓解好奇心的同时勒戒如何实现双向绑定  
> 为了便于说明原理与实现，本文相关代码主要摘自 **[vue源码](https://github.com/vuejs/vue)**,并进行了简化改造，相对比较简陋，并未考虑到数组的处理、数据的循环依赖等，也难免存在一些问题，欢迎大家指正。不过这些并不会影响大家的阅读和理解，相信看完本文后对大家的阅读vue源码的时候会更有帮助 

相信大家对mvvm双向数据都不应该陌生了，一言不合上代码，下面先看一个本文最终实现的效果吧，和vue一样的语法，如果还不了解双向绑定，猛戳[google](https://www.google.com.hk/search?q=%E5%8F%8C%E5%90%91%E7%BB%91%E5%AE%9A)
```html
<div id="mvvm-app">
    <input type="text" v-model="word">
    <p>{{word}}</p>
    <button v-on:click="sayHi">change model</button>
</div>

<script src="./js/observer.js"></script>
<script src="./js/watcher.js"></script>
<script src="./js/compile.js"></script>
<script src="./js/mvvm.js"></script>
<script>
    var vm = new MVVM({
        el: '#mvvm-app',
        data: {
            word: 'Hello World!'
        },
        methods: {
            sayHi: function() {
                this.word = 'Hi, everybody!';
            }
        }
    });
</script>
```

效果：
![](/images/mvvmshow.gif)
### 几种实现双向绑定的做法   
目前几种主流的mvc(vm)框架都实现了单向数据绑定，而我理所理解的数据绑定无非就是在单向绑定的基础上给可输入元素（input、textare等）添加了change(input)事件，来动态修改model和view，并没有多高深。所以无需太过介怀是实现的单向或双向绑定。   
   
实现数据绑定的做法有大致如下几种：
> 发布者-订阅者模式 (backbone.js)   
> 脏值检查 (angular.js)
> 数据劫持 (vue.js)

**发布者-订阅者模式**：一般通过sub,pub的方式实现数据和视图的绑定监听，更新数据方式通常做法是 *vm.set('property',value)* 这里有篇文章讲的比较详细，有兴趣可点[这里](http://www.html-js.com/article/Study-of-twoway-data-binding-JavaScript-talk-about-JavaScript-every-day)   
未完待续。。。。