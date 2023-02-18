1. 标签里的class是配合样式表的
   ```
   <div class="cssName">

    css中
   {
     .cssName:{
        color:red;
        font-weight:bold;
     }
   }
   ```  

1. a.visited{} 根据状态修改样式
2. 紧接在h1后面的p元素
3. h1 + p {
  font-size: 200%;
}
1. #a{} id选择器

1. 引用方式
   1. 首推外部
   2. 内建低效（style{}放在内部)
   3. 内联，每个元素单独写，不要用

1. @import 注入样式表
2. @作为注入是用时，@media(min-width:30em) 针对宽度大于30em的媒体

1. 伪类 a: hover{},指针悬浮在a元素上这个状态
2. 伪元素 a::first-line{}，选择元素中的第一行
3. 伪元素 a::before, a::after，更适合插入图标
4. 全局元素，不单独使用
   ```
   article *:first-child {

   }
   ```

1. 相对长度 em， 在 font-size 中使用是相对于父元素的字体大小，在其他属性中使用是相对于自身的字体大小，如 width
2. 百分比 如果字体设置百分比，那么是相对父类字体大小的百分比，如果容器设置百分比，就是相对它父类容器的百分比
3. 有些值不接受百分比，如只接受<length>，不接受<length-percent>
4. rgb设置第四个通道只会让颜色不透明，旧版IE不支持HSL，HSL可以调节饱和度
5. 类选择器比元素选择器有更高的优先级
6. 继承
```
body {
    color: green;
}
          
.my-class-1 a {
    color: inherit;//开启继承
}
          
.my-class-2 a {
    color: initial;//使用初始值，不被其他影响
}
          
.my-class-3 a {
    color: unset;//重置为默认值
}
    revert-layer //重置为上一个层叠层中应用的值
```

1. 层叠
   1. 资源顺序，同权重时后设置的覆盖之前的
   2. 优先级
   3. 重要程度
   4. !important 强行修改优先级, 自己少用，阅读他人代码时看懂即可
   5. @layerName 提高层的优先级

1. 响应式设计