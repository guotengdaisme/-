## 一、css重要知识点

### 1、text-align本质： 

​	是对于行内级元素来设置位置

### 2、子代选择器：

​	选择器以空格分隔：选择的是元素的所有子代

​	选择器以 > 分隔： 选择的是元素的直接子代 

### 3、伪类

​	伪类是元素选择器的一种，它用于选择元素的特定状态

### 4、伪元素::before, ::after (: 和:: 都可以)

​	伪元素中的content属性不可以省略，content中可以插入图片和文本。插入图片用url()函数。

### 5、选择器的权重

​	*！important: 10000*

​	*内联样式： 1000*

​	*id选择器： 100*

​	*类选择器，伪类： 10*

​	*元素选择器： 1*

​	*通配符： 0*

### 6、display值的特性

- block元素：

  独占父元素的一行

  可以随意设置宽高

  高度默认由内容决定

- inline-block元素：

  - 跟其他行内级元素在同一行显示

  - 可以随意设置宽高

  - 可以这样理解

    对外来说，它是一个行内级元素

    对内来说，它是一个块级元素

- inline:

  - 跟其他行内级元素在同一行显示
  - 不可以所以设置宽高
  - 宽高都有内容决定

### 7、元素的隐藏

- alpha：只是设置当前color和bgc的颜色透明度，不会影响其子元素。
- opacity: 设置透明度，会携带所有子元素都有一定的透明度

## 二、盒子模型

### 1、上下margin的传递

- margin-top传递
- margin-bottom传递
- 如何防止出现传递问题
  - 给父元素设置padding
  - 给父元素设置border
  - 出发BFC: 设置overflow：hidden

### 2、上下margin的折叠

- 当给两个块级元素设置margin-top和margin-bottom时，两个块级元素之间的距离并不会叠加，而是会取最大的值来作为两个块级元素之间的距离

### 3、box-sizing: border-box

- 正常为盒子设置宽高，是设置盒子的内容宽高，如果增加padding， border属性，会增大盒子的宽高，
- 如果不想更改盒子的高度，设置box-sizing:border-box,这样设置盒子的宽高就是元素的宽高，不再是内容的高度

### 4、行内非替换元素（a, span等）的注意事项

- 设置内边距和border后， 行内非替换元素的上下会被撑起来，但是不占用空间。
- 设置margin后， 行内非替换元素的上下margin不会生效

### 5、元素的居中方案

- 行内级元素：在父元素中使用text-align：center
- 块级元素 有宽度： 使用margin：0 auto;

### 6、单行显示省略号

```css
white-space: nowrap;
over-flow: hidden;
text-overflow: ellipsis;
```

### 7、多行显示省略号

```css
over-flow: hidden;
text-overflow: ellipsis;
display: -webkit-box;
-webkit-line-clamp: 2;//显示两行文字
-webkit-box-orient: vertical;

```

## 三、HTML高级元素

### 1、表格中的单元格合并

- 跨行合并: rowspan
- 跨列合并: colspan

## 四、结构伪类

### 1、nth-child(n):

​	 查找第n个子元素

### 2、nth-last-child(): 

​	 从后面开始查找

### 3、nth-of-type(): 

​	只计算查找同种类型的元素

## 五、额外知识补充

### 1、border画三角形

```css
width: 100px;
height: 100px;
box-sizing: border-box;
border: 50px solid transparent;
border-top-color: red;
```

### 2、使用网络字体

- 先通过字体网站下载字体
- 然后通过@font-face 来引用字体，并且设置格式
- 最后通过 font-family来使用字体 

```css
 @font-face {
        src: url("./font/AaGuXianTi-2.ttf");
        font-family: "gtd";
      }

      .test {
        font-family: "gtd";
      }
```

### 3、使用字体图标

```html
<!DOCTYPE html>
//先去下载icon
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <!-- <style>
      /* @font-face {
        src: url("./font02/iconfont.ttf");
        font-family: "gtd";
      }
      i {
        font-family: "gtd";
        font-style: normal;
      } */
    </style> -->
    <link rel="stylesheet" href="./font02/iconfont.css" />
  </head>

  <body>
    <i class="iconfont icon-add"></i>
  </body>
</html>
```

## 六、元素定位

### 1、relative 相对定位

- 相对于元素原来位置进行定位

### 2、fixed 固定定位

- 相对于视口进行定位

### 3、absolute 绝对定位

- 元素脱离文档流
- 定位参照对象是相邻最近的祖先元素的定位元素
- 如果找不到这样的祖先元素，则参照视口
- 定位元素
  - 不包含position：static

### 4、absolute和fixed的特点（一）

- 可以随意设置宽高（即使是行内非替换元素也可以设置宽高）
- 宽高默认由内容决定
- 不再受标准流的约束
- 不再给父元素汇报宽高数据（如果父元素不设置宽高的话，将不会显示宽高）
- 脱标元素内部默认仍然是标准流布局

### 5、absolute 和 fixed绝对定位元素的特点（二）

- 对于绝对定位元素来说：
  - 定义参照对象的宽度=left+right+mr+ml+绝对定位元素实际占用的宽度
  - 定义参照对象的高度=top+bottom+mt+mb+绝对定位元素实际占用的高度
- 绝对定位元素的宽高和定义参照对象的宽高一样
  - 设置 top bottom left right margin 都为0
- 绝对定位元素在参照对象中居中显示
  - 设置top bottom left right为0 margin设置为auto
  - 并且还要给绝对定位元素设置一定的宽高，要小于定义参照对象的宽高

### 6、sticky 粘性定位

- 当元素滚动到我们设置的位置时，就会变成固定定位
- sticky是相对于最近的滚动祖先包含滚动视口的

### 7、css属性：z-index

- z-index是用来设置**定位元素**之间的层叠顺序的
- 比较原则
  - 如果是兄弟进行比较元素值越大，层级越大
  - 如果不是兄弟元素，从相邻元素以及祖先元素中，找出最邻近的2个定位元素

## 七、float 浮动元素

### 1、浮动规则

- 元素浮动后会脱离标准流
- 浮动元素之间不能层叠
- 浮动元素不与行内级级元素，块级元素中的文字内容层叠

### 2、补充

- 元素之间的border边框重叠可以用margin-left或margin-right来解决

## 八、float元素高度塌陷

### 1、原因

- 由于浮动元素脱离了标准流，所以不会给父元素汇报自己的高度
  - 所以父元素在计算总高度的时候，就不会计算子元素的高度，导致高度塌陷
- 解决高度塌陷问题的过程，叫做清浮动

### 2、解决办法

- 给父元素设置高度（不推荐）

- 在父元素的最后增加一个块级元素，并设置clear：both;

- 给父元素添加伪元素来清除浮动

  ```css
  .clear-fix::after {
  	content: "";
      display: block;/*伪元素默认是非替换的行内级元素,						所以变为块级元素*/
      clear: both;
      
      visibility: hidden;/*兼容*/
      height: 0;/*兼容*/
  }
  
  .clear-fix {
  	*zoom: 1;/*ie6-7兼容*/
  
  }
  ```




## 九、flex布局

### 1、当子元素变成flex item 时的特点

- 将受flex container 的设置来进行布局
- flex item将不再严格区分行内级元素和块级元素
- flex item默认情况下是包裹内容的，但是可以设置宽度和高度

### 2、如何解决flex布局中justify-content最后一行的布局问题

![](C:\Users\der\Desktop\Snipaste_2022-04-14_12-13-02.png)

- **通过给flex-item的最后设置span或i元素，并给他们设置宽度来解决，添加的个数为flex-item的列数减去2**

## 十、形变和动画和vertical-align

### 1、transform:针对于行内级非替换元素不生效

- translate（x, y）: 百分比针对于自身的盒子大小
- scale（）： 缩放
- rotate（）： 旋转
- transform-origin：x，y ； 更改坐标原点的位置

### 2、水平居中和垂直居中方案总结

- 水平居中
  - 行内级元素：设置父元素的text-align： center
  - 块级元素： 设置当前元素的margin： 0 auto;
  - 绝对定位：当前元素有宽度的情况下，设置l0,r0,margin：0 auto  （定义参照对象的宽度=left+right+mr+ml+绝对定位元素实际占用的宽度）
  - flex: 设置 justify-content： center
- 垂直居中
  - 绝对定位：当前元素有高度的情况下，设置t0,b0,margin：auto   0（定义参照对象的高度=top+bottom+mt+mb+绝对定位元素实际占用的高度）
  - flex布局： 给父元素设置align-item： center
  - transform： 相对定位，top： 50%（这里不用margin-top是因为百分比相对的是父元素的宽度）transform： translate（0， -50%）（这里的百分比针对的是本身）

### 3、transition动画

- transition-property：指定应用过渡属性的名称
  - all
  - none
  - css属性名
- transition-duration：指定过渡动画所需的时间
- transition-timing-function：指定动画的变化曲线
- transition-delay： 指定过度动画执行之前的等待时间

### 4、animation

- 先通过@keyframes 定义每一帧要做的事情，之后通过animation来 引入这个帧动画

### 5、vertical-align

- 用来设置行盒内的行内级元素的对齐方式，默认是baseline，也就是基线对齐 
- 属性值：
  - baseline
  - top：给盒子自身设置，把行内级盒子的顶部根line-box对齐
  - middle： 行内级盒子的中心点与父盒基线加上x-height一半的线对齐
  - bottom
- 解决图片下边缘的间隙方法
  - 设置成top/middle/bottom
  - 将图片设置为block元素

### 6、行内块级元素的居中和分离现象

```css
.box .small {
    display: inline-block;
    width: 100px;
    height: 100px;
    line-height: 100px;
    /* 如果不设置行高的话，会继承父盒子的行高300px，导致行盒的高度超过了自身盒子的大小(因为内容要在行盒内进行居中)，导致了盒子的分离 */
    background-color: yellow;
  }
```

## 十一、HTML5新增特性和css补充

### 1、HTML5新增全局属性data-*

- 进行dom操作，通过dataset属性来获取，用于HTML和js之间的数据传递

### 2、var函数

- 可以使用css中的变量
- css中的变量只能被后代元素使用

### 3、calc函数

- calc()函数允许在声明css属性值时执行一些计算
  - 计算支持加减乘除的运算，+和-运算符要有空格
- 通常用来设置一些元素的尺寸或者位置

### 4、blur函数

- 用来设置高斯模糊
- 通常会和filter（给图片本身添加模糊）和backdrop-filter（给文件背景添加模糊度）这两个属性使用

### 十二、BFC和媒体查询

### 1、BFC

- 在同一个BFC中，相邻的两个box的上下margin会进行折叠
- 垂直方向上一个挨一个排列
- 垂直方向上的间距有margin决定
- 左边缘紧挨着包含块的左边缘

### 2、BFC 解决margin高度塌陷

### 3、BFC解决浮动元素父元素高度塌陷问题

- BFC的高度是auto时，是这样计算高度的
  - 如果有浮动元素， 那么会增加高度用来包含这些浮动元素的下边缘

### 4、媒体查询

- 在某些特定的情况下，通过特定的属性，去做对应情况的事
- 媒体的类型
- 媒体的特性
- 媒体特性之间的连接符

## 十三、css单位

### 1、绝对定位

- px
- in
- ...

### 2、相对定位

- em: 相对于自己本身font-size的大小，1em = font-size=15px
- rem：相对于根元素的font-size的大小
- vw/vh: 相对于 视口宽度的1%/视口高度的1%

## 十四、css预处理器

### 1、less

- 语法

  - 定义变量：@变量名： 值

  - 选择器的嵌套

  - &：当前选择器的父级

  - 运算： 以最左侧的单位为准

  - 混入：混入也可以传递参数，参考函数传参

    - 混入和映射结合使用

    ```css
    .mixed（@width： 100px） {
    
    	width: @width;
    
        height: 100px;
    
        background: red;
    
    }
    
    .box {
    	.mixed(10px)
    }
    .map {
    	width: 100px;
        height: 100px;
    
    }
    .box2 {
    	width: .map()[width];/*拿到混入的width的值*/
    }
    ```

    

## 十五、移动端视口

### 1、布局视口

- 默认情况下，一个pc端的网页在移动端会如何显示呢
  - 他会按照宽度为980px来布局一个页面的盒子和内容
  - 为了可以完整的显示在页面中，对整个页面进行缩小
- 相对于980px这个视口，称之为**布局视口**

### 2、视觉视口

- 显示在可见区域内的视口，就是视觉视口

## 3、理想视口

- 布局视口等于视觉视口时，就是理想视口
- 想要布局视口等于视觉视口，可以设置meta标签里的content属性