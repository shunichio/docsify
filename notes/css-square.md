### HTML代码

```html
<div class='square'>
	<div class='content'>
		<p>正方形内的内容</p>
	</div>
</div>
```

### CSS代码

```css
.square{
  position: relative;
  width: 20%;
  height: 0;
  padding-bottom: 20%;
  margin: 0 auto;
  background: #ccc;
}
.content{
  position: absolute;
  width: 100%;
  height: 100%;
}
```