## css 过度动画 transition

> transition: [property] [duration] [delay] [timing-function];

```css
transition-property: all; //过度属性
transition-duration: 0.5s; // 持续时间
transition-delay: 1s; // 延迟时间
transition-timing-function: linear; // 缓动函数
```

### transition-property

**通常是简写形式的第一个属性，规定应用过渡的 CSS 属性的名称。比如要修改 background ，就可以在这里写 background 。也可以使用 all ，所有适用的 CSS 过渡的属性上创建过渡效果（不是所有的属性都可以创建过渡效果的）。**

### transition-duration

**transition-duration 表示过渡效果的持续时间。transition-duration 设置成 3s 的持续时间是指设置 3 倍的 1000ms。**

### transition-delay

**transition-delay 表示过渡效果的延迟时间。transition-delay 设置成 3s 的延迟时间是指设置 3 倍的 1000ms。**

### transition-timing-function

**transition-timing-function 定义过渡效果的速度曲线。**
