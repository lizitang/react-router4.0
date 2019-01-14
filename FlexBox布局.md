### flexbox布局
阅读react native文档时，看到flex布局，有一些可能有点忘记，记录一下
在css中，justify-content决定了其子元素在主轴的排列方式，有以下几个属性:
+ `flex-start`
+ `flex-end`
+ `center`
+ `space-between`
+ `space-around`
+ `space-evenly`

前面3个属性顾名思义，分别是位于容器的开头，容器的结尾，以及容器的中心，子元素之间没有间隙
space-between：项目位于各行之间留有空白的容器内(2侧没有空隙),`大白话就是把第一个元素的位置与行起始位置对齐，最后一个元素与行结束位置对齐，中间的剩余空间平均分布`
![blockchain](https://images2018.cnblogs.com/blog/1110419/201805/1110419-20180514230719413-1623793759.png "flex")
space-around：项目位于各行之前、之间、之后都留有空白的容器内(2侧有空隙，且2侧的空隙为中间容器间空隙的一半)。
下面来举栗子进行说明：
在一个横向布局上，假设有三个div，每个宽度为定宽apx，如果想使两侧宽度为x,中间div间间隔为2x。x可以自适应。如下图：
![blockchain](https://images2018.cnblogs.com/blog/1110419/201805/1110419-20180514224732430-703478749.jpg "flex")
两行代码搞定：
```css
 .flex-container{
     display:flex;
     justify-content:space-around;
 }
```
如果中间不是2x，是x呢，也就是每个间隙与两侧的间隔相同。也就是这样：
![blockchain](https://images2018.cnblogs.com/blog/1110419/201805/1110419-20180514230915123-2103328164.png "flex")
这个时候就可以用到`space-evenly`这个属性了，但是这个属性的缺陷在于兼容性不是很好
