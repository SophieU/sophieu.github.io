---
title: 《CSS揭秘》Demo笔记
date: 2019-07-08
tag: CSS
categories: H5
---
> 《CSS揭秘》一书揭示了 47 个鲜为人知的 CSS 技巧，主要内容包括背景与边框、形状、 视觉效果、字体排印、用户体验、结构与布局、过渡与动画等。本书将带领读者循序渐进地探寻更优雅的解决方案，攻克每天都会遇到的各种网页样式难题。
- DRY: don't repeat yourself,减少代码重复
- WET：we enjoy typing/write everything twice,与DRY正相反
<!--more-->
<img src="https://img1.doubanio.com/view/subject/l/public/s28659699.jpg" style="width:200px;"/>

## 一、背景与边框
- [半透明边框](http://dabblet.com/gist/012289cc14106a1bd7a5) 
    - 原理：利用`background-clip:padding-box`实现背景色不占用边框底部（默认情况下background-clip的初始值是border-box，这意味着当边框设置半透明色值时，显示的会是背景色叠加透明色的效果）
- [多重边框](http://dabblet.com/gist/525eb8e9cdade71723c1)
    - 原理：1、利用`box-shadow`可以创建任意数量的投影（实色即边框），但注意投影是不占用元素布局的。 2、利用`outline`实现双重边框
- [灵活背景定位](http://dabblet.com/gist/0f226e63595d1bef88cb)
    - 原理：`background-positioon:right 10px bottom 20px;`指背景位置距离右边10px，距离底部20px.
- [background-origin](http://dabblet.com/gist/0f19ac5d28d0aa7b6c60)
    - 作用：用于设置背景的原始位置依据，值为content-box,padding-box,border-box;
- [calc()方案](http://dabblet.com/gist/b5fcb42d055427ab6c1a)
- [边框内圆角](http://dabblet.com/gist/170fe436f290083cc24c)
    - 原理：利用outline和box-shadow组合实现内圆角（outline目前没有实现圆角）
    - 方法二：两个元素来实现，当然这样增加了额外的html标签
- [横向条纹背景](http://dabblet.com/gist/119dbf7f0c76dba7b7ee)
    - 原理：利用`bacground:linear-gradient(red 50%,green 50%)`实现双条纹，利用`background-size:100% 30px`实现单个条纹高15px
- [垂直条纹背景](http://dabblet.com/gist/1b4983062fd2b4d7e60e)
    - 原理：设置`linear-gradient`角度为90deg，或`to right`，通过`background-size:30px 100%`设置条纹单个宽15px
- [斜向条纹](http://dabblet.com/gist/abeab80934fc26e6538e)
    - 原理：45deg斜向条纹，利用 勾股定理计算出 条纹若30px时，需要background-size对应的值
- [60度斜向条纹](http://dabblet.com/gist/5646121210b0c99c94a7)
    - 原理：通过`background:repeating-linear-gradient(60deg,red,red 15px,blue 15px ,blue 30px)`实现
- [灵活同色系条纹](http://dabblet.com/gist/aa50d296a8ec07cf7334)
    - 应用场景：如bootstrap的进度条，由深色背景+同色系浅背景的斜条纹组成
    - 原理：同色系深色作为背景色，再通过叠加`白色半透明`和透明条纹，实现深浅间隔条纹
- [蓝色网格背景](http://dabblet.com/gist/43bde2086e9d549fb844)
    - 原理：利用background-image:linear-gradient()支持分隔叠加的属性方式
- [波点背景图](http://dabblet.com/gist/cd33da5d1a48046746cd)
    - 原理：利用`background:radial-gradient(green 30%,transparent 0)`实现，径向渐变由内向外
- [棋盘](http://dabblet.com/gist/4f7ca0749582b1bd7527)
- [svg实现棋盘](http://dabblet.com/gist/5d3cacead6f134da842e)
- [角向渐变](http://dabblet.com/gist/1617f9750e4581e73720)
    - 作用：可用于实现取色的色轮
- [伪随机背景](http://dabblet.com/gist/4b4ad05f4c1b5259066f)
    - 作用：实现随机宽度的竖直条纹
- [动态loading图](http://dabblet.com/gist/ea53f3bee9a7f1439aa7)
    - 原理：通过叠加多个animation，实现连续动画效果（旋转，变色，边框宽度增加）
- [连续的图像边框](http://dabblet.com/gist/c73fd4ea4b592a05c004)
    - 原理：通过叠加纯白背景（渐变实现，padding-box）和图像背景（border-box）
- [信封边缘背景](http://dabblet.com/gist/e3cfcb094e65d85df847)
    - 原理：通过叠加纯白渐变背景（padding-box)和-45deg条纹渐变（borderbox)
- [边框背景图实现信封边缘](http://dabblet.com/gist/0f6900d370f55ec9c975)
    - 原理：通过border-image值为线性渐变的条纹而实现
- [移动的虚线边框](http://dabblet.com/gist/f26dddc71730c3847153)
    - 原理：利用背景叠加实现虚线边框，通过animation实现背景移动产生动态效果
- [字体脚注效果](http://dabblet.com/gist/9483cc7012049f2c808e)
    - 原理：利用`border-image`属性加一条线性渐变产生的垂直条纹，`border-width`控制边框粗线，`渐变长度`来控制脚注长度

## 二、形状
- [椭圆](http://dabblet.com/gist/aada0b96b21ae3e45c1b)
    - 原理：使用`border-radius:50%`实现
- [半椭圆](http://dabblet.com/gist/e98d11da331bd9482bb0)
    - 原理：利用border-radius可以使用`/`分隔开水平和垂直方向上的圆角值特性来分别设置不同方式上的圆角半径
- [四分之一椭圆](http://dabblet.com/gist/2b75df0e72c9804e8abe) 
    - 原理：border-radius只设置一个角的圆弧
- [平行四边形](http://dabblet.com/gist/e1f72639c34a5578dda3)
    - 原理：`transform:skew()`变形。（注意：行内元素无效）
- [平行四边形伪元素方案](http://dabblet.com/gist/f2d98791ab1f0b238aa9)
    - 原理：利用伪元素来变形，从而不影响元素内部正常内容（变形元素内部的内容也会变形）
- [菱形图片裁切](http://dabblet.com/gist/7563400)
    - 原理：嵌套元素，内外反向rotate，内部图片宽度与对角线平齐.(内部反向rotate是为了摆正图片)
- [菱形裁切方案二](http://dabblet.com/gist/c62456fed36a524b8273)
    - 原理：利用`clip-path`裁切路径属性，设置其值为`polygon()`多边形来实现 
- [元素切角](http://dabblet.com/gist/2937c990d6bfad274740)
    - 原理：利用渐变（透明到实色）实现角度，利用background-size控制多个渐变互不影响实现多个切角
- [元素弧形切角](http://dabblet.com/gist/24484257bc6cf7076a8e)
    - 原理：利用径向渐变实现，多个圆角与上述同理
- [border-image实现切角](http://dabblet.com/gist/365c867441f3ee298212)
    - 原理：运用border-image结合svg图实现切角边框，通过background-clip限制背景色
- [裁切路径实现切角](http://dabblet.com/gist/4a88c463afdb0778e973)
    - 原理：运用clip-path:polygon()实现，局限兼容性不好，裁切会忽略内部文本内容。优点：可裁切任意内容，不受背景图限制
- [梯形导航tab效果](http://dabblet.com/gist/1345dc9399dc8e794502)
    - 原理：运用`transform:perspective()`实现穿透变形
- [动态饼图](http://dabblet.com/gist/722909b9808c14eb7300)
    - 原理：伪元素遮盖旋转
- [静态饼图](http://dabblet.com/gist/66e1e52ac2a44ad87aa4)
- [svg实现饼图](http://dabblet.com/gist/4696e4c6700fe9f346d8)

## 三、视觉效果
- [单侧投影](http://dabblet.com/gist/cc055dadf493c15723cf)
    - 原理：利用box-shadow的3个大小值外，第4个长度参数（扩张半径），当扩张半径为负的模糊半径时，即可实现单侧投影
- [邻边投影](http://dabblet.com/gist/d29d19ab66177b18bd64)
    - 原理：设置双侧位移，并设置扩张半径为模糊半径值一半
- [对侧投影](http://dabblet.com/gist/a23e445fca36293d12f6)
    - 原理：叠加两个单侧投影
- [不规则图形投影](http://dabblet.com/gist/d8a2376c79906d68f3d1)
    - 原理：利用CSS滤镜`filter:drop-shadow()`实现
- [染色效果](http://dabblet.com/gist/b338c9940a31b727b7a9)
    - 原理：通过多个滤镜结合实现效果
- [染色效果-混合模式](http://dabblet.com/gist/0dced2852818c0f555e9)
    - 原理：利用CSS混合模式属性，设置两层背景混合效果，mix-blend-mode:是混合叠加两个元素的效果，background-blend-mode是设置两层背景图的混合效果。`luminosity`表示混合模式
- [毛玻璃效果](http://dabblet.com/gist/d9f243ddd7dbffa341a4)
    - 原理：利用CSS滤镜`filter:blur()`实现模糊，通过模糊伪元素再叠加到元素底部实现。（细节：滤镜默认靠近边框时效果会减弱，因此在设置blur值时，最好多设大一点）
- [折角效果](http://dabblet.com/gist/83b4d6bc907aa5ab576a)
    - 原理：利用切角效果 ，结合叠加一个深色三角形
- [多角度折角效果](http://dabblet.com/gist/bc32dc20adea2261c731)
- [mixin实现折角](http://codepen.io/leaverou/pen/raGaNR)

## 四、字体排印
- [连字符断行](http://dabblet.com/gist/e370ba333ae95116e212)
    - 效果不理想（兼容性不好）
- [插入换行](http://dabblet.com/gist/7eb242f4e8f078cadbca)
    - 原理：运用unicode中的换行符`0x000A`,在CSS中即为`\000A`（简写为`\A`）来作为伪元素的content值，同时注意保留换行符和空白符`white-space:pre`
- [文本行斑马条纹](http://dabblet.com/gist/cf30f5d442533b32600d)
    - 原理：利用`条纹背景`实现条纹，再利用背景`em`值作为条纹大小
- [调整tab的宽度](http://dabblet.com/gist/88c1523dfd1284950b4d)
    - 原理：通过CSS3新增文本属性`tab-size:2;`来设置每个tab占2个字符宽度
- [连字](http://dabblet.com/gist/f9f078a639e5cad002d4)
- [华丽的&符号](http://dabblet.com/gist/42898081c1f7cf8750f9)
    - 原理：通过`@font-face`规则实现基本的字体嵌入
- [自定义下划线](http://dabblet.com/gist/58f3d67d5bb0f8338776)
    - 原理：CSS渐变 ，background-size,text-shadow,‘条纹背景’【效果不理想】
- [波浪下划线](http://dabblet.com/gist/c9184f634e9e9efde6e8)
    - 【效果不理想】
- [凸版印刷效果](http://dabblet.com/gist/80eafefae35d26979972)
- [空心字效果](http://dabblet.com/gist/7a665ce85190f9cb201e)
- [文字外发光效果](http://dabblet.com/gist/4af6d5c3b22395543cb8)
    - 原理：通过叠加多个text-shadow，运用transition过渡实现特效
- [文字突起效果](http://dabblet.com/gist/ba8161d038b9c13b99ac)
- [环形文字](http://dabblet.com/gist/bda1bf6b0c4adbcea63d)
    - 原理：通过内联SVG来实现文字环形排版，还可以运用动画使其旋转。通过`xlink:href`属性来把元素内容链接到路径上
    - 通过JS处理，把`.circual`类元素自动替换为相应的SVG

## 五、用户体验
- [选用合适的鼠标指针](http://dabblet.com/gist/41c8da81742e1ab1d07f)
    - 原理：在不同场景下选用不同的鼠标指针。`cursor`属性设置
    - 隐藏光标：`cursor:none`,对于老版本浏览器可以通过`cursor:url(transparent.gif)`来传入一张1*1的透明gif模拟隐藏
- [扩大可点击区域](http://dabblet.com/gist/df5686d4047307991f73)
    - 原理：通过伪元素扩大按钮范围
- [扩大可点击区域](http://dabblet.com/gist/d76ac0acc80923d47106)
- [自定义复选框](http://dabblet.com/gist/e269f10328615254e29e)
    - 原理：利用checkbox的`:checked`属性区分选中状态，再结合相邻label元素控制，通过伪元素实现想要的效果，再隐藏掉原复选框
- [开关式按钮](http://dabblet.com/gist/fa5c622180b232043891)
- [CSS遮罩层](http://dabblet.com/gist/91538b2131d3545035ca)
    - 原理：通过box-shadow实现。缺陷：遮罩层下还是可以操作
- [原生modal遮罩层](http://dabblet.com/gist/bb2121aea259be94c35d)
    - 原理：通过 `::backdrop`伪类实现遮罩
- [模糊背景弱化](http://dabblet.com/gist/1326eb460b0dff91d638)
    - 原理：通过滤镜模糊`filter:blur()`模糊背景
- [滚动提示](http://dabblet.com/gist/20205b5fcdd834461e80)
    - 应用场景：当可向下，或向上滚动时，提示出部分阴影
- [交互式图片对比控件](http://dabblet.com/gist/b7e7fef7dcf9a7161a51)

## 六、结构与布局
- [自适应内部元素](http://dabblet.com/gist/82eb1575806f6eff9c37)
    - 原理：通过CSS3新增属性`min-content`让元素宽度最大宽度为其内部不可断行的元素宽度，
- [精准控制表格列表](http://dabblet.com/gist/7979af102a991cecfcdf)
    - 原理：通过设置`table-layout:fixed`把表格更多的控制权交到开发者手里，而不是让浏览器决定内容多少时变更理想的渲染宽度
- [根据兄弟元素的数量范围来匹配元素](http://dabblet.com/gist/70c434a6e802b062f494)
    - 原理：选择器`:nth-child`,兄弟选择符`~`结合使用
- [满幅背景，定宽内容](http://dabblet.com/gist/763229b68fa27c5c1bfa)
    - 运用：通常情况下我们制作定宽内容版本居中时，会用到两个html元素，外层100%宽度，内层定宽并通过margin:auto居中。
    - 优化：此方案通过`纯CSS`的方法来实现，且只需要一个元素就可以了
- 垂直居中
    - [绝对定位解决方案](http://dabblet.com/gist/cd12fac0e18bb27fb62d)
        - 通过绝对定位结合translate实现水平垂直居中
    - [基于视口单位的解决方案](http://dabblet.com/gist/bf12b39d8f5da2b6e5b6)
        - 只针对相对于整屏视口来做水平垂直居中的效果。利用`margin:50vh auto 0`实现对上50vh，再通过transform:translateY(-50%);实现居中
    - [flexbox解决方案](http://dabblet.com/gist/8aa9aa04ee57f479c513)
- CSS-Sticky-footer紧贴底部页脚
    - [固定高度解决方案](http://dabblet.com/gist/b10c3eb3b7078711a588)
        - 原理：在已知页脚以及header高度的情况下，通过`calc(100vh-header-footer)`得到中间部分的min-height
    - [Flexbox解决方案](http://dabblet.com/gist/410e43c60863a8dba193)
        - 原理：通过弹性布局实现
    - 更多解决方案可以搜索`CSS-sticky-footer`，上面两种方法都提倡不增加额外的html（因为这会影响性能）

## 七、过渡与动画
- 动画调速函数
    - [弹跳球动画](http://dabblet.com/gist/1b37089310d0a5a2d8e6)
        - CSS在提供了内置了几种缓动函数`ease,linear,ease-in,ease-out,ease-in-out`外，还提供了`cubic-bezier()`用于开发者定定义调速函数。
        - [animate.css动画库](https://daneden.github.io/animate.css/)
    - [弹性过渡-popup动效](http://dabblet.com/gist/6cf33228089efef8a5ac)
    - [弹性过渡变色效果](http://dabblet.com/gist/73294faf1e2bc5ff0078)
- [逐帧动画-CSS实现Loading动态图](http://dabblet.com/gist/bcc082518391f45b41dc)
    - 原理：通过把所有帧全部拼合到一张PNG图中，以一个一帧大小元素来容纳，变换background-position值实现帧过度，通过animation的steps()函数实现不平滑过度，从而显示每帧动画
- []()
- []()
- []()
- []()
- []()