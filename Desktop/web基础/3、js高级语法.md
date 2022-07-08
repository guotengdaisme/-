## 一、v8引擎原理

- 简单来说就是将一段js代码，解析成ast抽象语法树，再通过ignation解释器成字节码，字节码通过编译器成为cpu可以识别的机器码
- v8在执行代码之前，会创建一个执行上下文栈（函数调用栈）
  - 在执行全局代码之前会在堆内存中创建全局上下文(GEC),  在里面有个VO这里的VO也就是GO
  - 到这里需要准备的就准备好了，开始执行代码

## 二、全局函数的执行过程

当执行代码时，遇到函数的执行时，会在函数调用栈中创建一个函数执行上下文，里面有个VO这里的VO是AO，

AO里保存了函数体里的变量和函数，在函数执行完成后，函数调用栈和AO对象销毁。函数本身保存在GO对象内是以地址保存的，这个地址指向的是函数存储空间，里面保存了scoped： parent scope

<img src="C:\Users\der\Desktop\Snipaste_2022-05-29_14-55-10.png" style="zoom:38%;" />

<img src="C:\Users\der\Desktop\web基础\image\全局代码执行（函数嵌套）.png" style="zoom:38%;" />

## 三、内存管理

### 1、内存分配

- js对于基本数据类型是分配在栈内存中的
- 对于复杂数据类型是分配在堆内存中的

### 2、js垃圾回收（GC）

- GC算法
  - 引用计数：每当一个对象有引用到它时，就技术加一；每当一个对象取消引用时，计数减一，当计数为0的时候，就清除它，不好的地方在于容易造成循环引用
  - 标记清除：设置一个根对象，垃圾回收器会定期从这个根开始，找到有引用到的对象，对于没有引用到的对象，就认为是不可用的对象。

## 四、数组中函数的使用

### 1、filter（）

- filter接收一个函数作为参数。这个函数接收三个参数item，index， arr，返回的值是Boolean类型，如果返回TRUE就把这个item返回出去加入到**新数组**里，FALSE的话就跳过这个item，如果没有找到，返回空数组，filter和find方法都不改变原数组

## 2、map（）

- map接收一个函数，函数里面接收一个参数，返回一个表达式。当你想对数组做一些处理时，比如每一项都乘以10，就可以return item * 10.用一个新数组接收即可。不修改原数组

## 3、find（）

- find接收一个函数，函数里面接收一个参数，返回一个表达式。当你想在数组中查找符合条件的项，就可以返回条件。比如，return item=10，这样就会返回数组中等于10 的item，这里是只要找到符合条件的，便不会继续遍历。如果没有找到符合的值，返回undefined，

### 4、reduce（）

- reduce接收两个参数，其中一个是函数，这个函数接收了两个参数，preValue，item。返回preValue+item用来做累加操作。另一个参数作为preValue的初始值。最后用一个变量接收最后累加的结果。

### 5、foreach()

- 遍历元素，接收一个函数作为参数，函数接收三个参数item，index，arr，最后一个参数是原数组，这个方法可能会修改原数组

### 6、slice（）

- 传入两个参数，start和end，用于截取数组，返回一个新数组。

## 五、闭包的内存泄漏

![](C:\Users\der\Desktop\web基础\image\闭包的内存泄漏.png)

## 六、call函数的实现

```js
Function.prototype.gtdCall = function (thisArg, ...arg) {
  const fn = this

  thisArg = (thisArg !== null && thisArg !== undefined) ? Object(thisArg) : Window
  thisArg.fn = fn
  var result = thisArg.fn(...arg)
  delete thisArg.fn
  return result
}

function foo(num) {
  console.log('函数被执行',this, num)
  return num
}

var result = foo.gtdCall({}, 123)
console.log(result)
```

## 七、apply函数的实现

```js
Function.prototype.tdApply = function (thisArg, argArray) {
  const fn = this

  thisArg = (thisArg !== null && thisArg !== undefined) ? Object(thisArg) : Window

  thisArg.fn = fn

  argArray = argArray || []
  
  thisArg.fn(...argArray)
  delete thisArg.fn
}

function foo(num1, num2) {
  console.log(this, num1, num2)
}

foo.tdApply({}, [20, 30])
```



## 八、bind函数的实现

```js
Function.prototype.tdBind = function(thisArg, ...argArray) {
  const fn = this

  thisArg = (thisArg !== null && thisArg !== undefined) ? Object(thisArg) : Window

  function proxyFn(...arg) {
    thisArg.fn = fn
    const fnArg = [...argArray, ...arg]
    const result = thisArg.fn(...fnArg)

    delete thisArg.fn

    return result
  }

  return proxyFn
}


function foo(...arg) {
  console.log(this, ...arg)
}

const result = foo.tdBind({}, 20, 30)

result(40, 50)
```

## 九、arguments转数组

1、通过for循环，来遍历arguments

2、通过array.prototype.slice.call(arguments)

3、es6语法array.from(arguments),

4、[...arguments]

5、箭头函数里没有arguments

## 十、对象的属性描述符

### 1、数据属性描述符

### 2、存取属性描述符

- 不想要对象的属性名时，可以通过存取属性描述符中的get，来设置别名
- 当你想要对一个属性对它的访问和设置值过程有一些操作，使用存取属性描述符

## 十一、创建对象的方案

### 1、工厂模式

![](C:\Users\der\Desktop\web基础\image\工厂模式.png)

缺点是：通过工厂模式创建的对象没有对应的具体类型

### 2、构造函数

- 构造函数和普通函数外表看起来没有区别，当一个函数被new调用了之后，就变成了构造函数
- 那么，new调用有什么特殊的地方呢
  
- ![](C:\Users\der\Desktop\web基础\image\new调用函数的特殊之处.png)
  
- 缺点是我们需要为每一个需要创建的对象，去写对应的构造函数

- ```js
  function Person (name, age) {
      this.name = name
      this.age = age
      
      this.swimming = function () {
  		console.log(this.name + '在跑步')
      }
  }
  
  const person = new Person('gtd', 22)
  console.log(person)
  
  ```

## 十二、原型

### 1、对象的原型

- js中每一个对象都有一个特殊的属性prototype，这个属性指向了另一个对象
  - 我们可以通过对象的__proto__属性来获取到这个特殊的对象

### 2、函数的原型

- 同样的每一个函数都有一个prototype属性
- 因为new操作符会让通过构造函数产生的对象，其中的prototype属性会被赋值为该构造函数的prototype属性
- 这样就意味着，我们通过一个构造函数来创建的对象的prototype属性都指向该构造函数的prototype
  - p1.__proto__ === Person.prototype
  - ![](C:\Users\der\Desktop\web基础\image\创建对象的内存表现.png)

### 3、重写原型对象

- 如果我们，需要在原型上添加很多属性，这时我们就可以对原型对象进行重写![](C:\Users\der\Desktop\web基础\image\重写原型对象.png)

### 4、构造函数和原型组合

![](C:\Users\der\Desktop\web基础\image\构造函数和原型组合.png)

## 十三、原型链和继承

### 1、原型链关系内存图

![]()![原型链关系图](C:\Users\der\Desktop\web基础\image\原型链关系图.png)

### 2、原型链

- 原型链的尽头的Object的原型，再使用__proto__向下查找时就会返回null，而且Object的原型中有很多属性
- Object是所有类的父类

### 3、继承

- 原型链实现继承

  - ```js
    function Person () {
      this.name = 'gtd'
    }
    
    Person.prototype.eating = function () {
      console.log(this.name + '：正在吃饭')
    }
    
    function Student () {
      this.age = 22
    }
    //将person的对象赋值给student的原型对象
    Student.prototype = new Person()
    
    Student.prototype.swimming = function () {
      console.log('swimming~')
    }
    
    var student = new Student()
    
    console.log(student.age)
    ```

  - 内存图![](C:\Users\der\Desktop\web基础\image\原型链实现继承内存图.png)

  - 缺点![](C:\Users\der\Desktop\web基础\image\原型链实现继承的缺点.png)

- 借用构造函数继承

  - ```js
    function Person (name, age) {
      //父类接收子类传过来的参数，并且赋值到this中（这里的this就是子类内部创建的对象）
      this.name = name
      this.age = age
    }
    
    Person.prototype.eating = function () {
      console.log(this.name + '：正在吃饭')
    }
    
    function Student (name, age) {
       //通过call绑定this（也就是student内部创建的对象）到父类中，给父类传入参数
      Person.call(this, name, age)
    }
    
    
    Student.prototype.swimming = function () {
      console.log('swimming~')
    }
    
    var student = new Student('gtd', 22)
    
    console.log(student)
    ```

  - 缺点

    - 调用了两次父类函数
    - 所有的子类实例都会拥有两份父类属性
  
- 寄生组合式继承

  - ```js
    function createObject(o) {
      function Fn() {
      }
      Fn.prototype = o
      return new Fn()
    }
    
    function inheritPrototype(subType, superType) {
      subType.prototype = createObject(superType.prototype)
      subType.prototype.constructor = subType
    }
    
    function Person (name, age) {
      this.name = name
      this.age = age
    }
    
    Person.prototype.eating = function () {
      console.log(this.name + '：正在吃饭')
    }
    
    function Student (name, age) {
      Person.call(this, name, age)
    }
    
    inheritPrototype(Student, Person)
    
    Student.prototype.swimming = function () {
      console.log('swimming~')
    }
    
    
    
    var student = new Student('gtd', 22)
    
    console.log(student.__proto__.constructor)
    ```

    

## 十四、class定义类

### 1、类的构造函数

- ![](C:\Users\der\Desktop\web基础\image\类的构造方法.png)

### 2、类的实例方法

- 实例方法可以直接写在class类的内部，会将实例方法放到实例的原型对象上

### 3、类的访问器方法

- get ， set

### 4、类的静态方法

- 使用是static 关键字来定义方法 
- 不要需要用实例对象来调用，直接用类去调用，以为是定义在类方法里面的

### 5、继承

- 通过extends来实现

### 6、super关键字

- 使用位置在子类的构造函数，子类的实例方法，子类的静态方法
- 注意在子类的构造函数中使用this或者返回默认对象之前，必须通过super调用父类的构造函数

## 十五、let、const和var的区别

### 1、let、const不允许重复声明变量

### 2、let、const的作用域提升

- 如果我们在使用let定义变量之前，访问变量，会报错，那么，是因为这个变量没有在执行代码之前定义吗？
- 并不是，其实和var一样，使用let定义变量时，这些变量是会在他们的词法环境实例化时定义出来，只是不允许访问，直到变量被赋值
- 那么let和const有作用域提升吗？
- 个人理解为没有进行作用域提升，虽然在执行上下文创建完成后，变量也被创建出来，但是却不能被访问，所以不能成为作用域提升

### 3、定义的变量在解析的时候保存位置不同

- 在全局，使用var定义变量，变量会被保存到Window上，但是let和const并不会
- 那保存到哪里？
- ![]()![执行上下文的描述](C:\Users\der\Desktop\web基础\image\执行上下文的描述.png)

- 在执行上下文中有个VE，我们定义的变量会被当做环境记录保存到VE中，这个VE，在V8引擎当中指向的是，用一种数据结构HashMap来设计的**Variable Map**来保存的let和const定义的变量

### 4、let和const的块级作用域

- var是没有块级作用域的
- ![](C:\Users\der\Desktop\web基础\image\let const的块级作用域.png)

- 在for-Switch-if语句中，使用let和const定义变量也会产生块级作用域

### 5、字符串模板

### 6、函数的默认参数

### 7、函数的剩余参数

- 如果最后一个参数是以...开头的，那么函数多余的值都会传进最后的参数中并且成为一个数组
- 剩余参数和arguments的区别
  - rest剩余参数只包含那些没有对应形参的实参，而arguments包含了所有实参
  - arguments是一个对象，类数组，而rest剩余参数是数组
  - 剩余参数的提出是希望来代替arguments的

### 8、展开语法

- 展开运算符**...**其实是一种浅拷贝

### 9、Symbol

- Symbol是es6新推出的一种基本数据结构
- 通过Symbol（）可以生成一个独一无二的字符，用来防止命名冲突

### 10、Set

- es6推出的一种新的数据结构Set，和数组类似，但是里面存储的值不能重复
- 创建Set通过new Set（）来创建
- set常见的属性
  - size：返回set中元素的个数
- set常见的方法
  - ![](C:\Users\der\Desktop\web基础\image\set常用方法.png)

### 十一、WeakSet

- weakset是和set类似的一种数据结构
- 那么，set和weakset有什么区别呢
  - weakset只能保存对象类型，不能保存基本数据类型
  - weakset是一种弱引用，如果没有其他引用对某个对象引用的话，将会被GC回收
  - weakSet不能被遍历
- 常见的方法
  - add
  - delete
  - has

### 十二、Map

- 事实上，我们只能有字符串来作为一个对象的key

- 但是在某种情况下我们想要用特殊的key，比如对象，这时候就可以使用Map

- ```js
  const obj = {
  	name: 'gtd'
  }
  const foo = {
  	age: 22
  }
  
  const map = new Map()
  map.set(obj, 'aaa')
  map.set(foo, 'bbb')
  
  const map = new Map([
      [obj, 'aaa'],
      [foo, 'bbb']
  ])
  
  ```

- map常用方法和属性![](C:\Users\der\Desktop\web基础\image\map常用方法.png)

### 十三、WeakMap

- map和weakmap的区别和set，weakset的区别一样
- ![](C:\Users\der\Desktop\web基础\image\weakmap常用方法.png)

- WeakMap的应用

  ```js
  const obj1 = {
    name: 'gtd',
    age: 22
  }
  function obj1Fn() {
    console.log("obj1")
  }
  
  
  const obj2 = {
    name: 'james',
    age: 38
  }
   
  function obj2Fn () {
    console.log('obj2')
  }
  
  const weakmap = new WeakMap()
  const map = new Map()
  map.set('name', obj1Fn)
  
  weakmap.set(obj1, map)
  
  const target = weakmap.get(obj1)
  const fns = target.get('name')
  
  fns()
  ```


## 十六、监听对象的操作

### 1、Object.defineProperty实现

- ```js
  const obj = {
    name: 'gtd',
    age: 22
  }
  Object.keys(obj).forEach((key) => {
    let value = obj[key]
    Object.defineProperty(obj, key, {
      get() {
        console.log('zhibeixiugai')
        return value
        
      },
      set(newValue) {
        console.log('beifuzhi')
        value = newValue
      }
    })
    
  })
  
  obj.name = 'james'
  console.log(obj.name)
  ```

### 2、Proxy结合Reflect实现

- ```js
  const obj = {
    name: 'gtd',
    age: 22
  }
  
  const newProxy = new Proxy(obj, {
    get(target, key) {
      console.log('获取值的操作')
      return Reflect.get(target, key)
    },
    set(target, key, newValue) {
      console.log('设置值的操作')
      Reflect.set(target, key, newValue)
    },
    has(target, key) {
      console.log('进行has操作')
      return Reflect.has(target, key)
    },
    deleteProperty(target, key) {
      console.log('进行删除操作')
      return Reflect.deleteProperty(target, key)
    }
  })
  newProxy.name = 'james'
  console.log(newProxy.name)
  console.log("name" in newProxy)
  delete newProxy.name
  
  ```

## 十七、Receiver的作用

![](C:\Users\der\Desktop\web基础\image\receiver的作用.png)

## 十八、响应式原理

### 1、vue3实现

- ```js
  let reactiveFns = null
  
  class Depend {
    constructor() {
      this.reactiveFns = new Set()
    }
  
    depend() {
      if(reactiveFns) {
        this.reactiveFns.add(reactiveFns)
      }
      
    }
    notify() {
      this.reactiveFns.forEach((item) => {
        item()
      })
    }
  }
  
  const targetMap = new WeakMap()
  function getDep(target, key) {
    let map = targetMap.get(target)
    if(!map) {
      map = new Map()
      targetMap.set(target, map)
    }
  
    let targetDep = map.get(key)
    if(!targetDep) {
      targetDep = new Depend()
      map.set(key, targetDep)
    }
  
    return targetDep
  }
  
  
  
  
  
  function reactive(obj) {
    return new Proxy(obj, {
      get(target, key, receiver) {
        const dep = getDep(target, key)
        dep.depend()
    
        return Reflect.get(target, key, receiver)
      },
      set(target, key, recevier) {
        
        Reflect.set(target, key, recevier)
        let dep = getDep(target, key)
        dep.notify()
      }
    })
  }
  
  
  const obj = reactive({
    name: 'gtd',
    age: 22
  })
  const foo = reactive({
    address: 'heilongjiang'
  })
  // const dep = new Depend()
  
  function watchFns(fn) {
    reactiveFns = fn
    reactiveFns()
    reactiveFns = null
  }
  
  watchFns(function() {
    console.log(obj.name + 'gtd')
  })
  
  watchFns(function () {
    console.log(foo.address)
  })
  
  
  obj.name = 'lilei'
  
  foo.address = 'jilin'
  
  
  ```

### 2、vue2实现

- 

```js
let reactiveFns = null

class Depend {
  constructor() {
    this.reactiveFns = new Set()
  }

  depend() {
    if(reactiveFns) {
      this.reactiveFns.add(reactiveFns)
    }
    
  }
  notify() {
    this.reactiveFns.forEach((item) => {
      item()
    })
  }
}

const targetMap = new WeakMap()
function getDep(target, key) {
  let map = targetMap.get(target)
  if(!map) {
    map = new Map()
    targetMap.set(target, map)
  }

  let targetDep = map.get(key)
  if(!targetDep) {
    targetDep = new Depend()
    map.set(key, targetDep)
  }

  return targetDep
}





function reactive(obj) {
  Object.keys(obj).forEach((key) => {
    let value = obj[key]
    Object.defineProperty(obj, key, {
      get: function () {
        const dep = getDep(obj, key)
        dep.depend()
        return value
      },
      set: function(newValue) {
        value = newValue
        const dep = getDep(obj, key)
        dep.notify()
      }
    })
    
  })
  return obj
}


const obj = reactive({
  name: 'gtd',
  age: 22
})

const foo = reactive({
  address: 'heilongjiang'
})
// const dep = new Depend()

function watchFns(fn) {
  reactiveFns = fn
  reactiveFns()
  reactiveFns = null
  
}

watchFns(function() {
  console.log(obj.name + 'gtd')
})

watchFns(function () {
  console.log(foo.address)
})

obj.name = 'lilei'


foo.address = 'jilin'


```

## 十九、Promise

### 1、什么是Promise

- Promise是一个类，接受一个回调函数executor
- 这个函数会被立刻执行，并且这个函数接收两个回调函数resolve，reject
- 当resolve执行时，会执行Promise对象的then方法传入的回调函数
- 当reject执行时，会执行Promise对象的catch传入的回调函数

### 2、resolve不同值的区别

- ![](C:\Users\der\Desktop\web基础\image\resolve传入不同值区别.png)

### 3、Promise的对象方法--then

- then方法是有返回值的，返回的是一个新的Promise
- then的回调函数的返回值存在三种情况
  - 返回一个普通的值
    - 会将这个值传入到新的Promise的resolve，作为resolve的参数使用
  - 返回一个Promise
    - then返回的Promise的状态 会由 then回调函数返回的Promise决定
  - 返回一个实现了then方法的对象
    - then返回的Promise的状态 会由 then回调函数返回的对象中的then 来决定

### 4、Promise的对象方法--catch

- 我们可以在then后面接着调用catch，这里的调用是原有的promise不是then返回的promise
- 当then的回调函数返回错误时，then后面的catch才会被then返回的promise调用
- catch方法也会返回一个Promise，在catch后面调用then和catch，只要没有抛出错误，还是会执行then

### 5、Promise的对象方法--finally

- 无论Promise是出于fulfilled还是rejected状态，都会执行的方法

### 6、Promise的类方法--resolve，reject

- Promise.resolve等价于new Promise((resolve )=> {resolve()})
- Promise.reject等价于 new Promise((reject) => {reject()})

### 7、Promise的类方法--all

- Promise.all()相当于将所有的Promise的放到一起，返回一个新Promise，只有传入的promise状态变成fulfilled，新的Promise状态才会变成fulfilled，并且将所有Promise的返回值，放到一个数组，做为新的返回值

- 当有一个Promise状态为reject时，新的Promise状态为reject，并且会将第一个reject的返回值作为参数；

  ```js
  const p1 = new Promise()
  const p2 = new Promise()
  const p3= new Promise()
  
  Promise.all(p1, p2, p3).then()
  ```

### 8、Promise的类方法--allSettled

- all方法有一个缺陷：当有其中一个Promise变成reject状态时，新Promise就会立即变成对应的reject状态

- 那么对于resolved的，以及依然处于pending状态的Promise，我们是获取不到对应的结果的；

- 在ES11（ES2020）中，添加了新的API Promise.allSettled： 

  - 该方法会在所有的Promise都有结果（settled），无论是fulfilled，还是reject时，才会有最终的状态；

  - 并且这个Promise的结果一定是fulfilled的；

- allSettled的结果是一个数组，数组中存放着每一个Promise的结果，并且是对应一个对象的；

### 9、Promise的类方法--race、any

- Promise.race() 多个Promise竞争谁先出结果，就用谁的
- Promise.any() es12新推出的方法，会等待一个fulfilled状态的Promise，然后决定新的Promise的状态
- 如果都是reject状态，也会等待所有的Promise变为reject，才会返回一个错误

## 二十、事件循环-微任务-宏任务

### 1、浏览器事件循环

- js引擎在执行代码的时候，是从上到下依次来执行的，在这个过程中，如果遇到异步请求，会交给其他的线程去执行，当需要执行其他线程中的代码时，就会去事件队列中加入要执行的任务，当js引擎执行完同步代码后，js引擎就会从事件队列中取出要执行的代码，这就是事件循环。
- ![](C:\Users\der\Desktop\web基础\image\浏览器的事件循环.png)

### 2、微任务和宏任务

- 事件队列中维护这两个队列
  - 宏任务队列： 定时器，Ajax，Dom监听等
  - 微任务队列： Promise的then回调，queueMicrotask()等

- 那么这两个执行队列的顺序是怎样的？
  - 1、mainscript先执行
  - 2、当执行每一个宏任务时，都要看看微任务队列是否为空，只有微任务队列为空，才可以执行
  - 如果微任务队列不为空，就要先执行微任务

## 二十一、处理异步请求的几种方式

- ```js
  function requestData(url) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(url)
      }, 2000);
    })
  }
  // 需求：我们需要向服务器发送送三次请求
  // 发送第一次请求，拿到结果，发送第二次请求依赖第一次请求的结果
  // 发送第三次请求，依赖前两次的结果
  // 第一种方法：缺点：回调地狱，代码结构不清晰
  // function getData() {
  //   requestData('gtd').then(res => {
  //     requestData(res + 'aaa').then(res => {
  //       requestData(res + 'bbb').then(res => {
  //         console.log(res)
  //       })
  //     })
  //   })
  // }
  // getData()
  
  // 第二种方法： 缺点：代码结构不清晰
  
  // function getData() {
  //   requestData('gtd').then(res => {
  //     return new Promise(resolve => {
  //       resolve(res + 'aaa')
  //     })
  //   }).then(res => {
  //     return new Promise(resolve => {
  //       resolve(res + 'bbb')
  //     }).then(res => {
  //       console.log(res)
  //     })
  //   })
  // }
  // getData()
  
  // 第三种方法：生成器
  
  // function* getData() {
  //   const res1 = yield requestData('gtd')
  //   const res2 = yield requestData(res1 + 'aaa')
  //   const res3 = yield requestData(res2 + 'bbb')
  //   console.log(res3)
  // }
  
  // const generator = getData()
  
  // generator.next().value.then(res => {
  //   generator.next(res).value.then(res => {
  //     generator.next(res).value.then(res => {
  //       console.log(res)
  //     })
  //   })
  // })
  
  // 封装一个自己执行的生成器的函数
  // function execGenerator(gerenatorFn) {
  //   const generator = gerenatorFn()
  
  //   function exec(res) {
  //     let result = generator.next(res)
  //     if(result.done) return
  //     result.value.then(res => {
  //       exec(res)
  //     })
  //   }
  //   exec()
  
  // }
  // execGenerator(getData)
  
  // 也可以使用co库
  
  // const co = require('co')
  // co(getData)
  
  // 第四种方法： async await 相当于是第三种方法的语法糖
  
  async function getData() {
    const res1 = await requestData('gtd')
    const res2 = await requestData(res1 + 'aaa')
    const res3 = await requestData(res2 + 'bbb')
    console.log(res3)
  }
  
  getData()
  ```

##  二十二、async await

### 1、async 

- async用于声明一个异步函数
- 异步函数都会返回一个Promise
- 异步函数的执行流程：
  - 异步函数的内部执行过程和普通函数是一样的，也会同步执行
- 异步函数的返回值和普通函数的区别：
  - 异步函数的返回值会加入Promise的resolve中
  - 返回值是Promise时，Promise的resolve会由返回值的Promise决定
  - 返回值是thenable时，Promise的resolve会由返回值的thenable决定
- 如果我们在异步函数中抛出错误时，会作为Promise的reject传递

### 2、await关键字

- async函数可以在内部使用await关键字，await后面通常跟一个表达式
- 只有await后面的Promise状态变为fulfilled时，也就是执行resolve后，才会继续向下执行代码
- 而执行resolve会调用then方法，所以就默认await后面的代码是加入到then的回调函数里执行的
- 执行await后面的promise会返回一个值，这个值就是promise中的resolve方法传入的值

### 3、await跟着其他的值

- await后面跟着普通的值，await 123，就相当于在promise中resolve（123）
- await后面跟着是Promise时，Promise的resolve会由返回值的Promise决定
- await后面跟着是thenable时，Promise的resolve会由返回值的thenable决定

