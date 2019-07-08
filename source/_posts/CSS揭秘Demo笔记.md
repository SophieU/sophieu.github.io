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