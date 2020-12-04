```css
.container {
  display: flex;

  /* 主轴方向 */
  flex-direction: row;

  /* 主轴是否换行 */
  flex-wrap: wrap;

  /* 主轴方向及换行的简写 */
  flex-flow: row wrap;

  /* 主轴方向的对齐方式 */
  justify-content: center;

  /* 交叉轴的对齐方式 */
  align-items: center;

  /* 主轴与交叉轴等多条轴的对齐方式 */
  align-content: center;
}

.item {
  /* 子元素的排列顺序 */
  order: 0;

  /* 子元素的放大比例 */
  flex-grow: 0;

  /* 子元素的缩小比例 */
  flex-shrink: 0;

  /* 子元素在容器分配多余空间之前，项目占据的主轴空间 */
  flex-basis: auto;

  /* 放大比例、缩小比例、主轴空间三者的缩写 */
  flex: 0 0 auto;

  /* 交叉轴方向独有的对齐方式，如果没有定义，则继承align-items的值 */
  align-self: center;
}
```