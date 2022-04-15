### rem
rem是一个灵活的、可扩展的单位，由浏览器转化像素并显示
相对于浏览器的根元素（HTML元素）的font-size
例如uni-app的upx\rpx
### text-align
对齐“块级元素的内部内容”
影响对齐方式
- left 把文本排列到左边。默认值：由浏览器决定。
- right 把文本排列到右边。
- center 把文本排列到中间。
- justify 实现两端对齐文本效果。
- inherit 规定应该从父元素继承 text-align 属性的值。
### Flex布局
弹性布局
容器的属性：

    flex-direction 主轴的方向
    flex-wrap 决定换行规则
    flex-flow
    justify-content 对齐方式，平行于主轴的对齐方式
    align-items 对齐方式，垂直于轴线的方向
    align-content

元素的属性：
    order 定义项目的排列顺序，顺序越小，排列越靠前，默认为0
    flex-grow 定义项目的放大比例，即使存在空间也不会放大
    flex-shrink 缩小比例，空间不足会等比例缩小 0不缩小
    flex-basis 分配多余的空间，项目占据的空间
    flex grow shrink basis 0 1 auto 的缩写
    align-self 允许单个项目与其他项目不一样的对齐方式
    align-items 默认属性为auto 继承父元素的align-items
