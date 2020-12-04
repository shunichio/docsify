**1、在 JavaScript 中，函数就是一个对象，函数在没有返回值时默认返回一个对象。**

**2、当一个对象 A（this object）没有某个方法，但是其他对象 B（other object）有这一方法，我们可以借助 call()方法或 apply()方法，用其它对象 B（other object）的方法来操作。apply()方法与 call()方法的第一个参数是对象 A（this object），第二个参数为传递给对象 B（other object）的该方法的参数。**

**3、形式为：B.fn.apply(A,[arguments])或者 B.fn.call(A,arg1,arg2,arg3……)**

```javascript
function person() {}
person.prototype = {
    name: 'jack',
    say: function() {
        console.log('my name is' + this.name);
    }
};
var goodPerson = new person();
goodPerson.say(); // 打印 my name is jack
```

现在有一个对象`dog = {name:'wangwang'}`,这条狗也想说话（拥有 say 方法），那么我们可以这样`goodPerson.say.call(dog)`这样 dog 就可以说话了。

```javascript
var dog = {
    name: 'wangwang'
};
goodPerson.say.apply(dog); // my name is wangwang

goodPerson.say.call(dog); // my name is wangwang
```
