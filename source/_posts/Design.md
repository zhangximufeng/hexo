---
title: Javascript常用的设计模式详解
categories: 前端
tags: 
    - 技术
    - 架构
    - 设计模式
    - JavaScript
    - 开发
---

# 工厂模式
   工厂模式类似于现实生活中的工厂可以产生大量相似的商品，去做同样的事情，实现同样的效果;这时候需要使用工厂模式。

   简单的工厂模式可以理解为解决多个相似的问题;这也是她的优点;比如如下代码： 
   ```javascript
    function CreatePerson(name,age,sex) {
            var obj = new Object();
            obj.name = name;
            obj.age = age;
            obj.sex = sex;
            obj.sayName = function(){
                return this.name;
            }
            return obj;
    }
    var p1 = new CreatePerson("longen",'28','男');
    var p2 = new CreatePerson("mufeng",'27','女');
    console.log(p1.name); // longen
    console.log(p1.age);  // 28
    console.log(p1.sex);  // 男
    console.log(p1.sayName()); // longen

    console.log(p2.name);  // mufeng
    console.log(p2.age);   // 27
    console.log(p2.sex);   // 女
    console.log(p2.sayName()); // mufeng

    // 返回都是object 无法识别对象的类型 不知道他们是哪个对象的实列
    console.log(typeof p1);  // object
    console.log(typeof p2);  // object
    console.log(p1 instanceof Object); // true
   ```
   如上代码：函数CreatePerson能接受三个参数name,age,sex等参数，可以无数次调用这个函数，每次返回都会包含三个属性和一个方法的对象。
<!-- more -->
工厂模式是为了解决多个类似对象声明的问题;也就是为了解决实列化对象产生重复的问题。

优点：能解决多个相似的问题。

缺点：不能知道对象识别的问题(对象的类型不知道)。

复杂的工厂模式定义是：将其成员对象的实列化推迟到子类中，子类可以重写父类接口方法以便创建的时候指定自己的对象类型。

 父类只对创建过程中的一般性问题进行处理，这些处理会被子类继承，子类之间是相互独立的，具体的业务逻辑会放在子类中进行编写。

 父类就变成了一个抽象类，但是父类可以执行子类中相同类似的方法，具体的业务逻辑需要放在子类中去实现；比如我现在开几个自行车店，那么每个店都有几种型号的自行车出售。我们现在来使用工厂模式来编写这些代码;

父类的构造函数如下：
```javascript
// 定义自行车的构造函数
var BicycleShop = function(){};
BicycleShop.prototype = {
    constructor: BicycleShop,
 
    sellBicycle: function(model){
     /*
      * 买自行车这个方法
      * @param {model} 自行车型号
      */
        var bicycle = this.createBicycle(mode);
        // 执行A业务逻辑
        bicycle.A();

        // 执行B业务逻辑
        bicycle.B();

        return bicycle;
    },
    createBicycle: function(model){
        throw new Error("父类是抽象类不能直接调用，需要子类重写该方法");
    }
};
```
上面是定义一个自行车抽象类来编写工厂模式的实列，定义了createBicycle这个方法，但是如果直接实例化父类，调用父类中的这个createBicycle方法,会抛出一个error，因为父类是一个抽象类，他不能被实列化，只能通过子类来实现这个方法，实现自己的业务逻辑，下面我们来定义子类，我们学会如何使用工厂模式重新编写这个方法，首先我们需要继承父类中的成员，然后编写子类;如下代码：
```javascript
// 定义自行车的构造函数
var BicycleShop = function(name){
    this.name = name;
    this.method = function(){
        return this.name;
    }
};
BicycleShop.prototype = {
    constructor: BicycleShop,
  
    sellBicycle: function(model){
    /*
     * 买自行车这个方法
     * @param {model} 自行车型号
     */
            var bicycle = this.createBicycle(model);
            // 执行A业务逻辑
            bicycle.A();

            // 执行B业务逻辑
            bicycle.B();

            return bicycle;
        },
        createBicycle: function(model){
            throw new Error("父类是抽象类不能直接调用，需要子类重写该方法");
        }
    };
    // 实现原型继承
    function extend(Sub,Sup) {
        //Sub表示子类，Sup表示超类
        // 首先定义一个空函数
        var F = function(){};

        // 设置空函数的原型为超类的原型
        F.prototype = Sup.prototype; 

        // 实例化空函数，并把超类原型引用传递给子类
        Sub.prototype = new F();
                    
        // 重置子类原型的构造器为子类自身
        Sub.prototype.constructor = Sub;
                    
        // 在子类中保存超类的原型,避免子类与超类耦合
        Sub.sup = Sup.prototype;

        if(Sup.prototype.constructor === Object.prototype.constructor) {
            // 检测超类原型的构造器是否为原型自身
            Sup.prototype.constructor = Sup;
        }
    }
    var BicycleChild = function(name){
        this.name = name;
// 继承构造函数父类中的属性和方法
        BicycleShop.call(this,name);
    };
    // 子类继承父类原型方法
    extend(BicycleChild,BicycleShop);
// BicycleChild 子类重写父类的方法
BicycleChild.prototype.createBicycle = function(){
    var A = function(){
        console.log("执行A业务操作");    
    };
    var B = function(){
        console.log("执行B业务操作");
    };
    return {
        A: A,
        B: B
    }
}
var childClass = new BicycleChild("龙恩");
console.log(childClass);
```
console.log(childClass.name);  // 龙恩

// 下面是实例化后 执行父类中的sellBicycle这个方法后会依次调用父类中的A

// 和B方法；A方法和B方法依次在子类中去编写具体的业务逻辑。

childClass.sellBicycle("mode"); // 打印出  执行A业务操作和执行B业务操作

上面只是"龙恩"自行车这么一个型号的，如果需要生成其他型号的自行车的话，可以编写其他子类，工厂模式最重要的优点是：可以实现一些相同的方法，这些相同的方法我们可以放在父类中编写代码，那么需要实现具体的业务逻辑，那么可以放在子类中重写该父类的方法，去实现自己的业务逻辑；使用专业术语来讲的话有2点：第一：弱化对象间的耦合，防止代码的重复。在一个方法中进行类的实例化，可以消除重复性的代码。第二：重复性的代码可以放在父类去编写，子类继承于父类的所有成员属性和方法，子类只专注于实现自己的业务逻辑。
# 单例模式   
单例模式提供了一种将代码组织为一个逻辑单元的手段，这个逻辑单元中的代码可以通过单一变量进行访问。

单例模式的优点是：

可以用来划分命名空间，减少全局变量的数量。
使用单例模式可以使代码组织的更为一致，使代码容易阅读和维护。
可以被实例化，且实例化一次。
什么是单例模式？单例模式是一个用来划分命名空间并将一批属性和方法组织在一起的对象，如果它可以被实例化，那么它只能被实例化一次。

但是并非所有的对象字面量都是单例，比如说模拟数组或容纳数据的话，那么它就不是单例，但是如果是组织一批相关的属性和方法在一起的话，那么它有可能是单例模式，所以这需要看开发者编写代码的意图；

下面我们来看看定义一个对象字面量(结构类似于单例模式)的基本结构如下：
```javascript
// 对象字面量
var Singleton = {
    attr1: 1,
    attr2: 2,
    method1: function(){
        return this.attr1;
    },
    method2: function(){
        return this.attr2;
    }
};
```
如上面只是简单的字面量结构，上面的所有成员变量都是通过Singleton来访问的，但是它并不是单例模式；因为单例模式还有一个更重要的特点，就是可以仅被实例化一次，上面的只是不能被实例化的一个类，因此不是单例模式；对象字面量是用来创建单例模式的方法之一；

使用单例模式的结构如下demo

我们明白的是单例模式如果有实例化的话，那么只实例化一次，要实现一个单例模式的话，我们无非就是使用一个变量来标识该类是否被实例化，如果未被实例化的话，那么我们可以实例化一次，否则的话，直接返回已经被实例化的对象。

**如下代码是单例模式的基本结构：**   
```javascript
// 单例模式
var Singleton = function(name){
    this.name = name;
    this.instance = null;
};
Singleton.prototype.getName = function(){
    return this.name;
}
// 获取实例对象
function getInstance(name) {
    if(!this.instance) {
        this.instance = new Singleton(name);
    }
    return this.instance;
}
// 测试单例模式的实例
var a = getInstance("aa");
var b = getInstance("bb");
```
// 因为单例模式是只实例化一次，所以下面的实例是相等的

console.log(a === b); // true

由于单例模式只实例化一次，因此第一次调用，返回的是a实例对象，当我们继续调用的时候，b的实例就是a的实例，因此下面都是打印的是aa；

console.log(a.getName());// aa

console.log(b.getName());// aa

上面的封装单例模式也可以改成如下结构写法：
```javascript
// 单例模式
var Singleton = function(name){
    this.name = name;
};
Singleton.prototype.getName = function(){
    return this.name;
}
// 获取实例对象
var getInstance = (function() {
    var instance = null;
    return function(name) {
        if(!instance) {
            instance = new Singleton(name);
        }
        return instance;
    }
})();
// 测试单例模式的实例
var a = getInstance("aa");
var b = getInstance("bb");
```
// 因为单例模式是只实例化一次，所以下面的实例是相等的

console.log(a === b); // true

console.log(a.getName());// aa

console.log(b.getName());// aa

理解使用代理实现单列模式的好处
    比如我现在页面上需要创建一个div的元素，那么我们肯定需要有一个创建div的函数，而现在我只需要这个函数只负责创建div元素，其他的它不想管，也就是想实现单一职责原则，就好比淘宝的kissy一样，一开始的时候他们定义kissy只做一件事，并且把这件事做好，具体的单例模式中的实例化类的事情交给代理函数去处理，这样做的好处是具体的业务逻辑分开了，代理只管代理的业务逻辑，在这里代理的作用是实例化对象，并且只实例化一次; 创建div代码只管创建div，其他的不管；如下代码：
```javascript
// 单例模式
var CreateDiv = function(html) {
    this.html = html;
    this.init();
}
CreateDiv.prototype.init = function(){
    var div = document.createElement("div");
    div.innerHTML = this.html;
    document.body.appendChild(div);
};
// 代理实现单例模式
var ProxyMode = (function(){
    var instance;
    return function(html) {
        if(!instance) {
            instance = new CreateDiv("我来测试下");
        }
        return instance;
    } 
})();
var a = new ProxyMode("aaa");
var b = new ProxyMode("bbb");
console.log(a===b);// true
```
理解使用单例模式来实现弹窗的基本原理

下面我们继续来使用单例模式来实现一个弹窗的demo；我们先不讨论使用单例模式来实现，我们想下我们平时是怎么编写代码来实现弹窗效果的; 比如我们有一个弹窗，默认的情况下肯定是隐藏的，当我点击的时候，它需要显示出来；如下编写代码：
```javascript
// 实现弹窗
var createWindow = function(){
    var div = document.createElement("div");
    div.innerHTML = "我是弹窗内容";
    div.style.display = 'none';
    document.body.appendChild('div');
    return div;
};
document.getElementById("Id").onclick = function(){
    // 点击后先创建一个div元素
    var win = createWindow();
    win.style.display = "block";
}
```
如上的代码；大家可以看看，有明显的缺点，比如我点击一个元素需要创建一个div，我点击第二个元素又会创建一次div，我们频繁的点击某某元素，他们会频繁的创建div的元素，虽然当我们点击关闭的时候可以移除弹出代码，但是呢我们频繁的创建和删除并不好，特别对于性能会有很大的影响，对DOM频繁的操作会引起重绘等，从而影响性能；因此这是非常不好的习惯；我们现在可以使用单例模式来实现弹窗效果，我们只实例化一次就可以了；如下代码：
```javascript
// 实现单例模式弹窗
var createWindow = (function(){
    var div;
    return function(){
        if(!div) {
            div = document.createElement("div");
            div.innerHTML = "我是弹窗内容";
            div.style.display = 'none';
            document.body.appendChild(div);
        }
        return div;
    }
})();
document.getElementById("Id").onclick = function(){
    // 点击后先创建一个div元素
    var win = createWindow();
    win.style.display = "block";
}
```
理解编写通用的单例模式

上面的弹窗的代码虽然完成了使用单例模式创建弹窗效果，但是代码并不通用，比如上面是完成弹窗的代码，假如我们以后需要在页面中一个iframe呢？我们是不是需要重新写一套创建iframe的代码呢？比如如下创建iframe：
```javascript
var createIframe = (function(){
    var iframe;
    return function(){
        if(!iframe) {
            iframe = document.createElement("iframe");
            iframe.style.display = 'none';
            document.body.appendChild(iframe);
        }
        return iframe;
    };
})();
```
我们看到如上代码，创建div的代码和创建iframe代码很类似，我们现在可以考虑把通用的代码分离出来，使代码变成完全抽象，我们现在可以编写一套代码封装在getInstance函数内，如下代码：
```javascrpt
var getInstance = function(fn) {
    var result;
    return function(){
        return result || (result = fn.call(this,arguments));
    }
};
```
如上代码：我们使用一个参数fn传递进去，如果有result这个实例的话，直接返回，否则的话，当前的getInstance函数调用fn这个函数，是this指针指向与这个fn这个函数；之后返回被保存在result里面；现在我们可以传递一个函数进去，不管他是创建div也好，还是创建iframe也好，总之如果是这种的话，都可以使用getInstance来获取他们的实例对象；

如下测试创建iframe和创建div的代码如下：
```javascript
// 创建div
var createWindow = function(){
    var div = document.createElement("div");
    div.innerHTML = "我是弹窗内容";
    div.style.display = 'none';
    document.body.appendChild(div);
    return div;
};
// 创建iframe
var createIframe = function(){
    var iframe = document.createElement("iframe");
    document.body.appendChild(iframe);
    return iframe;
};
// 获取实例的封装代码
var getInstance = function(fn) {
    var result;
    return function(){
        return result || (result = fn.call(this,arguments));
    }
};
// 测试创建div
var createSingleDiv = getInstance(createWindow);
document.getElementById("Id").onclick = function(){
    var win = createSingleDiv();
    win.style.display = "block";
};
// 测试创建iframe
var createSingleIframe = getInstance(createIframe);
document.getElementById("Id").onclick = function(){
    var win = createSingleIframe();
    win.src = "http://cnblogs.com";
};
```
# 模块模式
我们通过单例模式理解了是以对象字面量的方式来创建单例模式的；比如如下的对象字面量的方式代码如下：
```javascript
var singleMode = {
    name: value,
    method: function(){
                
    }
};
//模块模式的思路是为单例模式添加私有变量和私有方法能够减少全局变量的使用；如下就是一个模块模式的代码结构：

var singleMode = (function(){
    // 创建私有变量
    var privateNum = 112;
    // 创建私有函数
    function privateFunc(){
        // 实现自己的业务逻辑代码
    }
    // 返回一个对象包含公有方法和属性
    return {
        publicMethod1: publicMethod1,
        publicMethod2: publicMethod1
    };
})();




```
模块模式使用了一个返回对象的匿名函数。在这个匿名函数内部，先定义了私有变量和函数，供内部函数使用，然后将一个对象字面量作为函数的值返回，返回的对象字面量中只包含可以公开的属性和方法。这样的话，可以提供外部使用该方法；由于该返回对象中的公有方法是在匿名函数内部定义的，因此它可以访问内部的私有变量和函数。

我们什么时候使用模块模式？

如果我们必须创建一个对象并以某些数据进行初始化，同时还要公开一些能够访问这些私有数据的方法，那么我们这个时候就可以使用模块模式了。

理解增强的模块模式

增强的模块模式的使用场合是：适合那些单列必须是某种类型的实例，同时还必须添加某些属性或方法对其加以增强的情况。比如如下代码：
```javascript
function CustomType() {
    this.name = "mufeng";
};
CustomType.prototype.getName = function(){
    return this.name;
}
var application = (function(){
    // 定义私有
    var privateA = "aa";
    // 定义私有函数
    function A(){};

    // 实例化一个对象后，返回该实例，然后为该实例增加一些公有属性和方法
    var object = new CustomType();

    // 添加公有属性
    object.A = "aa";
    // 添加公有方法
    object.B = function(){
        return privateA;
    }
    // 返回该对象
    return object;
})();


```
下面我们来打印下application该对象；如下：

console.log(application);

继续打印该公有属性和方法如下：

console.log(application.A);// aa

console.log(application.B()); // aa

console.log(application.name); // mufeng

console.log(application.getName());// mufeng
# 代理模式
   代理是一个对象，它可以用来控制对本体对象的访问，它与本体对象实现了同样的接口，代理对象会把所有的调用方法传递给本体对象的；代理模式最基本的形式是对访问进行控制，而本体对象则负责执行所分派的那个对象的函数或者类，简单的来讲本地对象注重的去执行页面上的代码，代理则控制本地对象何时被实例化，何时被使用；我们在上面的单体模式中使用过一些代理模式，就是使用代理模式实现单体模式的实例化，其他的事情就交给本体对象去处理；

代理的优点：

代理对象可以代替本体被实例化，并使其可以被远程访问；
它还可以把本体实例化推迟到真正需要的时候；对于实例化比较费时的本体对象，或者因为尺寸比较大以至于不用时不适于保存在内存中的本体，我们可以推迟实例化该对象；
我们先来理解代理对象代替本体对象被实例化的列子；比如现在京东ceo想送给奶茶妹一个礼物，但是呢假如该ceo不好意思送，或者由于工作忙没有时间送，那么这个时候他就想委托他的经纪人去做这件事，于是我们可以使用代理模式来编写如下代码：
```javascript
// 先申明一个奶茶妹对象
var TeaAndMilkGirl = function(name) {
    this.name = name;
};
// 这是京东ceo先生
var Ceo = function(girl) {
    this.girl = girl;
    // 送结婚礼物 给奶茶妹
    this.sendMarriageRing = function(ring) {
        console.log("Hi " + this.girl.name + ", ceo送你一个礼物：" + ring);
    }
};
// 京东ceo的经纪人是代理，来代替送
var ProxyObj = function(girl){
    this.girl = girl;
    // 经纪人代理送礼物给奶茶妹
    this.sendGift = function(gift) {
        // 代理模式负责本体对象实例化
        (new Ceo(this.girl)).sendMarriageRing(gift);
    }
};
// 初始化
var proxy = new ProxyObj(new TeaAndMilkGirl("奶茶妹"));
proxy.sendGift("结婚戒"); // Hi 奶茶妹, ceo送你一个礼物：结婚戒
```
代码如上的基本结构，TeaAndMilkGirl 是一个被送的对象(这里是奶茶妹)；Ceo 是送礼物的对象，他保存了奶茶妹这个属性，及有一个自己的特权方法sendMarriageRing 就是送礼物给奶茶妹这么一个方法；然后呢他是想通过他的经纪人去把这件事完成，于是需要创建一个经济人的代理模式，名字叫ProxyObj ；他的主要做的事情是，把ceo交给他的礼物送给ceo的情人，因此该对象同样需要保存ceo情人的对象作为自己的属性，同时也需要一个特权方法sendGift ，该方法是送礼物，因此在该方法内可以实例化本体对象，这里的本体对象是ceo送花这件事情，因此需要实例化该本体对象后及调用本体对象的方法(sendMarriageRing).

最后我们初始化是需要代理对象ProxyObj；调用ProxyObj 对象的送花这个方法(sendGift)即可；

对于我们提到的优点，第二点的话，我们下面可以来理解下虚拟代理，虚拟代理用于控制对那种创建开销很大的本体访问，它会把本体的实例化推迟到有方法被调用的时候；比如说现在有一个对象的实例化很慢的话，不能在网页加载的时候立即完成，我们可以为其创建一个虚拟代理，让他把该对象的实例推迟到需要的时候。

理解使用虚拟代理实现图片的预加载

在网页开发中，图片的预加载是一种比较常用的技术，如果直接给img标签节点设置src属性的话，如果图片比较大的话，或者网速相对比较慢的话，那么在图片未加载完之前，图片会有一段时间是空白的场景，这样对于用户体验来讲并不好，那么这个时候我们可以在图片未加载完之前我们可以使用一个loading加载图片来作为一个占位符，来提示用户该图片正在加载，等图片加载完后我们可以对该图片直接进行赋值即可；下面我们先不用代理模式来实现图片的预加载的情况下代码如下：

第一种方案：不使用代理的预加载图片函数如下
```javascript
// 不使用代理的预加载图片函数如下
var myImage = (function(){
    var imgNode = document.createElement("img");
    document.body.appendChild(imgNode);
    var img = new Image();
    img.onload = function(){
        imgNode.src = this.src;
    };
    return {
        setSrc: function(src) {
            imgNode.src = "http://img.lanrentuku.com/img/allimg/1212/5-121204193Q9-50.gif";
            img.src = src;
        }
    }
})();
// 调用方式
myImage.setSrc("https://img.alicdn.com/tps/i4/TB1b_neLXXXXXcoXFXXc8PZ9XXX-130-200.png");
```
如上代码是不使用代理模式来实现的代码；

第二种方案：使用代理模式来编写预加载图片的代码如下：
```javascript
var myImage = (function(){
    var imgNode = document.createElement("img");
    document.body.appendChild(imgNode);
    return {
        setSrc: function(src) {
            imgNode.src = src;
        }
    }
})();
// 代理模式
var ProxyImage = (function(){
    var img = new Image();
    img.onload = function(){
        myImage.setSrc(this.src);
    };
    return {
        setSrc: function(src) {
                         myImage.setSrc("http://img.lanrentuku.com/img/allimg/1212/5-121204193Q9-50.gif");
        img.src = src;
        }
    }
})();
// 调用方式
ProxyImage.setSrc("https://img.alicdn.com/tps/i4/TB1b_neLXXXXXcoXFXXc8PZ9XXX-130-200.png");
```

第一种方案是使用一般的编码方式实现图片的预加载技术，首先创建imgNode元素，然后调用myImage.setSrc该方法的时候，先给图片一个预加载图片，当图片加载完的时候，再给img元素赋值，第二种方案是使用代理模式来实现的，myImage 函数只负责创建img元素，代理函数ProxyImage 负责给图片设置loading图片，当图片真正加载完后的话，调用myImage中的myImage.setSrc方法设置图片的路径；他们之间的优缺点如下：

第一种方案一般的方法代码的耦合性太高，一个函数内负责做了几件事情，比如创建img元素，和实现给未加载图片完成之前设置loading加载状态等多项事情，未满足面向对象设计原则中单一职责原则；并且当某个时候不需要代理的时候，需要从myImage 函数内把代码删掉，这样代码耦合性太高。
第二种方案使用代理模式，其中myImage 函数只负责做一件事，创建img元素加入到页面中，其中的加载loading图片交给代理函数ProxyImage 去做，当图片加载成功后，代理函数ProxyImage 会通知及执行myImage 函数的方法，同时当以后不需要代理对象的话，我们直接可以调用本体对象的方法即可；
从上面代理模式我们可以看到，代理模式和本体对象中有相同的方法setSrc,这样设置的话有如下2个优点：

用户可以放心地请求代理，他们只关心是否能得到想要的结果。假如我门不需要代理对象的话，直接可以换成本体对象调用该方法即可。
在任何使用本体对象的地方都可以替换成使用代理。
当然如果代理对象和本体对象都返回一个匿名函数的话，那么也可以认为他们也具有一直的接口；比如如下代码：
```javascript
var myImage = (function(){
    var imgNode = document.createElement("img");
    document.body.appendChild(imgNode);
    return function(src){
        imgNode.src = src; 
    }
})();
// 代理模式
var ProxyImage = (function(){
    var img = new Image();
    img.onload = function(){
        myImage(this.src);
    };
    return function(src) {
                myImage("http://img.lanrentuku.com/img/allimg/1212/5-121204193Q9-50.gif");
        img.src = src;
    }
})();
// 调用方式
ProxyImage("https://img.alicdn.com/tps/i4/TB1b_neLXXXXXcoXFXXc8PZ9XXX-130-200.png");
```
虚拟代理合并http请求的理解：

   比如在做后端系统中，有表格数据，每一条数据前面有复选框按钮，当点击复选框按钮时候，需要获取该id后需要传递给给服务器发送ajax请求，服务器端需要记录这条数据，去请求，如果我们每当点击一下向服务器发送一个http请求的话，对于服务器来说压力比较大，网络请求比较频繁，但是如果现在该系统的实时数据不是很高的话，我们可以通过一个代理函数收集一段时间内(比如说2-3秒)的所有id，一次性发ajax请求给服务器，相对来说网络请求降低了, 服务器压力减少了;

```html
<!-- 首先html结构如下： -->
<p>
    <label>选择框</label>
    <input type="checkbox" class="j-input" data-id="1"/>
</p>
<p>
    <label>选择框</label>
    <input type="checkbox" class="j-input" data-id = "2"/>
</p>
<p>
    <label>选择框</label>
    <input type="checkbox" class="j-input" data-id="3"/>
</p>
<p>
    <label>选择框</label>
    <input type="checkbox" class="j-input" data-id = "4"/>
</p>
```
一般的情况下 JS如下编写
```javascript
var checkboxs = document.getElementsByClassName("j-input");
    for(var i = 0,ilen = checkboxs.length; i < ilen; i+=1) {
        (function(i){
            checkboxs[i].onclick = function(){
                if(this.checked) {
                    var id = this.getAttribute("data-id");
                    // 如下是ajax请求
                }
            }
        })(i);
    }
```
下面我们通过虚拟代理的方式，延迟2秒，在2秒后获取所有被选中的复选框的按钮id，一次性给服务器发请求。

  通过点击页面的复选框，选中的时候增加一个属性isflag，没有选中的时候删除该属性isflag，然后延迟个2秒，在2秒后重新判断页面上所有复选框中有isflag的属性上的id，存入数组，然后代理函数调用本体函数的方法，把延迟2秒后的所有id一次性发给本体方法，本体方法可以获取所有的id，可以向服务器端发送ajax请求，这样的话，服务器的请求压力相对来说减少了。
```javascript
// 本体函数
var mainFunc = function(ids) {
    console.log(ids); // 即可打印被选中的所有的id
    // 再把所有的id一次性发ajax请求给服务器端
};
// 代理函数 通过代理函数获取所有的id 传给本体函数去执行
var proxyFunc = (function(){
    var cache = [],  // 保存一段时间内的id
        timer = null; // 定时器
    return function(checkboxs) {
        // 判断如果定时器有的话，不进行覆盖操作
        if(timer) {
            return;
        }
        timer = setTimeout(function(){
            // 在2秒内获取所有被选中的id，通过属性isflag判断是否被选中
            for(var i = 0,ilen = checkboxs.length; i < ilen; i++) {
                if(checkboxs[i].hasAttribute("isflag")) {
                    var id = checkboxs[i].getAttribute("data-id");
                    cache[cache.length] = id;
                }
            }
            mainFunc(cache.join(',')); // 2秒后需要给本体函数传递所有的id
            // 清空定时器
            clearTimeout(timer);
            timer = null;
            cache = [];
        },2000);
    }
})();
var checkboxs = document.getElementsByClassName("j-input");
for(var i = 0,ilen = checkboxs.length; i < ilen; i+=1) {
    (function(i){
        checkboxs[i].onclick = function(){
            if(this.checked) {
                // 给当前增加一个属性
                this.setAttribute("isflag",1);
            }else {
                this.removeAttribute('isflag');
            }
            // 调用代理函数
            proxyFunc(checkboxs);
        }
    })(i);
}
```
理解缓存代理：

   缓存代理的含义就是对第一次运行时候进行缓存，当再一次运行相同的时候，直接从缓存里面取，这样做的好处是避免重复一次运算功能，如果运算非常复杂的话，对性能很耗费，那么使用缓存对象可以提高性能;我们可以先来理解一个简单的缓存列子，就是网上常见的加法和乘法的运算。代码如下：
   ```javascript
    // 计算乘法
var mult = function(){
    var a = 1;
    for(var i = 0,ilen = arguments.length; i < ilen; i+=1) {
        a = a*arguments[i];
    }
    return a;
};
// 计算加法
var plus = function(){
    var a = 0;
    for(var i = 0,ilen = arguments.length; i < ilen; i+=1) {
        a += arguments[i];
    }
    return a;
}
// 代理函数
var proxyFunc = function(fn) {
    var cache = {};  // 缓存对象
    return function(){
        var args = Array.prototype.join.call(arguments,',');
        if(args in cache) {
            return cache[args];   // 使用缓存代理
        }
        return cache[args] = fn.apply(this,arguments);
    }
};
var proxyMult = proxyFunc(mult);
console.log(proxyMult(1,2,3,4)); // 24
console.log(proxyMult(1,2,3,4)); // 缓存取 24

var proxyPlus = proxyFunc(plus);
console.log(proxyPlus(1,2,3,4));  // 10
console.log(proxyPlus(1,2,3,4));  // 缓存取 10
   ```

   不行了。头有点疼。。未完待续。。。