## 导航栏滚动效果

```javascript
export default {
  data() {
    return {
      isFixed: false // 状态栏是否固定在顶部
    }
  },
  mounted() {
    window.addEventListener('scroll', this.handleScroll) // 添加监听事件，监听页面滚动
  },
  destroyed() {
    window.removeEventListener('scroll', this.handleScroll) // 移除监听，避免Bug
  },
  methods: {
    handleScroll() {
      const scrollTop =
        window.pageYOffset ||
        document.documentElement.scrollTop ||
        document.body.scrollTop
      if (scrollTop > 80) { // 导航栏高度为80px
        if (!this.isFixed) {
          this.isFixed = true
        }
      } else {
        this.isFixed = false
        document.getElementById('header').scrollTop = scrollTop
      }
    }
  }
}
</script>
```

`isFixed` 的值为 `true` 时，需要为导航栏添加的类名

```css
@keyframes fadeInDown {
  from {
    opacity: 0;
    transform: translate3d(0, -100%, 0);
  }

  to {
    opacity: 1;
    transform: translate3d(0, 0, 0);
  }
}
.header-fixed {
  position: fixed;
  background-color: rgba(0, 0, 0, 1);
}

.fadeInDown {
  animation-name: fadeInDown;
}
.animated {
  -webkit-animation-duration: 1s;
  animation-duration: 1s;
  -webkit-animation-fill-mode: both;
  animation-fill-mode: both;
}
```

## DOM 元素出现在视窗时，展现依次上浮的效果

**1、cdn 引入 `waypoints` 库,在 `nuxt.config.js` 文件的 `script` 选项中添加元素**

```javascript
script: [
  {
    src:
      'https://cdn.jsdelivr.net/npm/waypoints@4.0.1/lib/noframework.waypoints.js'
  }
];
```

**2、为需要效果的元素添加标识 `animation-box` 类，出现的效果，出现的时间等**

```html
<div
  class="animation-box"
  data-animate-effect="fadeIn"
  data-animate-duration="1000"
></div>
```

```javascript
<script>
export default {
  mounted() {
    this.scrollAnimate()
  },
  destroyed() {
    /* eslint-disable */
    Waypoint.destroyAll()
  },
  methods: {
    scrollAnimate() {
      const htmlEle = document.querySelectorAll('.animation-box') // 使用querySelectorAll() 方法得到dom元素数组
      htmlEle.forEach((element, index) => {
        /* eslint-disable */
        new Waypoint({
          element,
          handler(direction) { // 不可使用箭头函数，否则this指向不对
            const domCls = this.element.className.split(' ')
            const duration = Number(this.element.attributes['data-animate-duration'].textContent)
            const hasAnimationClass = domCls.indexOf('fadeIn') > -1 || domCls.indexOf('fadeInLeft') > -1 || domCls.indexOf('fadeInRight') > -1
            if (direction === 'down' && !hasAnimationClass) {
              const effectCls = this.element.attributes['data-animate-effect'].textContent
              setTimeout(() => {
                domCls.push(effectCls)
                this.element.style.visibility = 'visible'
                this.element.className = domCls.join(' ')
              }, duration)
            }
          },
          offset: 'bottom-in-view' // 元素底部与视窗（viewport）底部重合时调用 handler 函数
        })
      })
    }
  }
}
</script>
```

```css
.animation-box {
  visibility: hidden;
}
```

## 基本知识

### Flex 布局

```html
<section>
  <article>
    <h2>First article</h2>
  </article>

  <article>
    <h2>Second article</h2>
  </article>

  <article>
    <h2>Third article</h2>
  </article>
</section>
```

#### 容器的属性

```css
section {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  flex-flow: row wrap;
  justify-content: flex-start;
  align-items: flex-start;
  align-content: flex-start;
}
```

#### 项目的属性

```css
article {
  width: 200px;
  /* 排序 */
  order: 0;
  /* 无单位比例：等比放大。默认是0，不放大。如果设为1，则表示所有项目平均分配剩余的空间（容器总宽度减去所有项目的width、margin、padding值之后的值）*/
  flex-grow: 0;
  /* 无单位比例：等比缩小。默认是1，等比缩小。 如果设为0，表示不缩小*/
  flex-shrink: 1;
  /* 项目宽度最小值，默认值auto，项目本身的width值 */
  flex-basis: auto;
  /* flex-grow、flex-shrink、flex-basis 的缩写，auto 代表 1 1 auto， none 代表 0 0 auto。 */
  flex: auto;
  /* align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性 */
  align-self: auto;
}
```

### transition 属性

`transition` 属性是一个简写的 `CSS` 属性，按顺序依次代表的值为：`transition-property` 属性值，如： `width` 、 `margin-right` 等， `transition-duration` 过渡时间，`transition-timing-function` 过渡的线性函数，如： `ease-in` 、 `ease-out` 、 `ease-in-out` 、 `linear` 、 `cubic-bezier(0.1, 0.7, 1.0, 0.1)` 等，和 `transition-delay` 过渡延迟时间。

```css
.box {
  border-style: solid;
  border-width: 1px;
  display: block;
  width: 100px;
  height: 100px;
  background-color: #0000ff;
  -webkit-transition: width 2s, height 2s, background-color 2s, -webkit-transform
      2s;
  transition: width 2s, height 2s, background-color 2s, transform 2s;
}

.box:hover {
  background-color: #ffcccc;
  width: 200px;
  height: 200px;
  -webkit-transform: rotate(180deg);
  transform: rotate(180deg);
}
```

### transform 属性

```css
/* Function values */
transform: translate(12px, 50%);
transform: translateX(2em);
transform: translateY(3in);
transform: scale(2, 0.5);
transform: scaleX(2);
transform: scaleY(0.5);
transform: rotate(0.5turn);
transform: skew(30deg, 20deg);
transform: skewX(30deg);
transform: skewY(1.07rad);
transform: translate3d(12px, 50%, 3em);
transform: translateZ(2px);
transform: scale3d(2.5, 1.2, 0.3);
transform: scaleZ(0.3);
transform: rotate3d(1, 2, 3, 10deg);
transform: rotateX(10deg);
transform: rotateY(10deg);
transform: rotateZ(10deg);
transform: scale(0.5) translate(-100%, -100%);

/* Multiple function values */
transform: translateX(10px) rotate(10deg) translateY(5px);
```

### box-shadow 属性

```css
/* x偏移量 | y偏移量 | 阴影颜色 */
box-shadow: 60px -16px teal;

/* x偏移量 | y偏移量 | 阴影模糊半径 | 阴影颜色 */
box-shadow: 10px 5px 5px black;

/* x偏移量 | y偏移量 | 阴影模糊半径 | 阴影扩散半径 | 阴影颜色 */
box-shadow: 2px 2px 2px 1px rgba(0, 0, 0, 0.2);

/* 插页(阴影向内) | x偏移量 | y偏移量 | 阴影颜色 */
box-shadow: inset 5em 1em gold;

/* 任意数量的阴影，以逗号分隔 */
box-shadow: 3px 3px red, -1em 0 0.4em olive;
```

### box-sizing 属性

```css
/* 默认值，标准盒子模型。 width 与 height 只包括内容的宽和高， 不包括边框（border），内边距（padding），外边距（margin）。 尺寸计算公式：width = 内容的宽度，height = 内容的高度。宽度和高度都不包含内容的边框（border）和内边距（padding）。 */
box-sizing: content-box;

/* width 和 height 属性包括内容，内边距和边框，但不包括外边距。 width = border + padding + 内容的  width，
height = border + padding + 内容的 height。 */
box-sizing: border-box;
```
