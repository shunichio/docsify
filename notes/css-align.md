### HTML代码

```html
<div class="textShow">
  <ul class="clearfix">
    <li><span class="w2">导演</span>：</li>
    <li>快接啊杀手锏</li>
  </ul>
  <ul class="clearfix">
    <li><span class="w2">主演</span>：</li>
    <li>快接啊杀手锏</li>
  </ul>
  <ul class="clearfix">
    <li><span>地区语言</span>：</li>
    <li>快接啊杀手锏</li>
  </ul>
  <ul class="clearfix">
    <li><span class="w2">类型</span>：</li>
    <li>快接啊杀手锏</li>
  </ul>
  <ul class="clearfix">
    <li>
      <span>上映时间</span>：</li>
    <li>快接啊杀手锏</li>
  </ul>
</div>
```

### CSS代码

```css
.clearfix::before, .clearfix::after {
  display: table;
  content: '';
}

.clearfix::after {
  clear: both;
}

.clearfix {
  zoom: 1;
}

ul, li {
  margin: 0;
  padding: 0;
  list-style: none;
}

.textShow ul li {
  float: left;
  height: 24px;
  line-height: 24px;
}

.textShow ul li:first-child {
  width: 5em;
  overflow: hidden;
}

.w2 {
  letter-spacing: 2em; /*如果需要y个字两端对齐，则为(x-y)/(y-1),这里是（4-2）/(2-1)=2em */
  margin-right: -2em; /*同上*/
}

.w3 {
  letter-spacing: 0.5em;
  margin-right: -0.5em;
}
```


