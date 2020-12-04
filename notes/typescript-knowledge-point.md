# 基础类型

```typescript
// 布尔值
let isDone: boolean = false;
// 数字
let decLiteral: number = 6;
// 字符串
let name: string = 'bob';
// 模板字符串
let sentence: string = `Hello, my name is ${name}.

I'll be ${age + 1} years old next month.`;
// 数组：元素类型后面接上 []
let list: number[] = [1, 2, 3];
// 数组：使用数组泛型，Array<元素类型>
let list: Array<number> = [1, 2, 3];
// 元组：元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。
let x: [string, number];
x = ['hello', 10];
// 枚举：enum类型是对JavaScript标准数据类型的一个补充
enum Color {
    Red,
    Green,
    Blue
}
let c: Color = Color.Green; // 结果为： 1
// any 类型
let notSure: any = 4;
// void 类型： void类型像是与any类型相反，它表示没有任何类型。
let unusable: void = undefined;
```

有时候你会遇到这样的情况，你会比 TypeScript 更了解某个值的详细信息。 通常这会发生在你清楚地知道一个实体具有比它现有类型更确切的类型。这种情况就是 `类型断言`

通过 `类型断言` 这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 `类型断言` 好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript 会假设你，程序员，已经进行了必须的检查。

```typescript
// 尖括号语法
let someValue: any = 'this is a string';
let strLength: number = (<string>someValue).length;
// as语法
let someValue: any = 'this is a string';
let strLength: number = (someValue as string).length;
```

# 接口

TypeScript 的核心原则之一是对值所具有的结构进行类型检查。 在 TypeScript 里，接口的作用就是 **为这些类型命名和为你的代码或第三方代码定义契约** 。

### 为普通对象定义契约

```typescript
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    let newSquare = { color: 'white', area: 100 };
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

let mySquare = createSquare({ color: 'black' });

// 绕开额外的属性（接口中不存在的字面量）检查
// 一、类型断言
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);
// 二、字符串索引签名
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

### 描述函数类型

```typescript
// 为接口定义调用签名
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
// 函数参数名可以与接口定义的名字不同
mySearch = function(source: string, subString: string) {
    let result = source.search(subString);
    return result > -1;
};
```

### 继承接口

```typescript
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{}; // 类型断言

square.color = 'blue';
square.sideLength = 10;
square.penWidth = 5.0;
```

# 类

```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return 'Hello, ' + this.greeting;
    }
}

let greeter = new Greeter('world');
```

我们声明一个 `Greeter` 类。这个类有 3 个成员：一个叫做 `greeting` 的属性，一个构造函数 `constructor` 和一个 `greet` 方法。我们在引用任何一个类成员的时候都用了 `this` ， 它表示我们访问的是类的成员。最后一行，我们使用 `new` 构造了 `Greeter` 类的一个实例。 它会调用之前定义的构造函数，创建一个 `Greeter` 类型的新对象，并执行构造函数初始化它。

> 1、类是一个抽象的概念，它不存在于现实中的时间或空间里，类只是为所有的对象定义了抽象的属性与行为。就好像“Person（人）”这个类，它虽然可以包含很多个体，但它本身不存在于现实世界上。
> 2、对象是类的一个具体。它是一个实实在在存在的东西。
> 3、类是一个静态的概念，类本身不携带任何数据。当没有为类创建任何对象时，类本身不存在于内存空间中。
> 4、对象是一个动态的概念。每一个对象都存在着有别于其它对象的属于自己的独特的属性和行为。对象的属性可以随着它自己的行为而发生改变。

### 类的继承

```typescript
// 基类或者超类
class Animal {
    move(distanceInMeters: number = 0) {
        console.log(`Animal moved ${distanceInMeters}m.`);
    }
}
// 派生类或者子类
class Dog extends Animal {
    bark() {
        console.log('Woof! Woof!');
    }
}

const dog = new Dog();
dog.bark();
dog.move(10);
dog.bark();
```

```typescript
class Animal {
    name: string;
    constructor(theName: string) {
        this.name = theName;
    }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) {
        super(name); // 调用超类具有相同形参的构造函数
    }

    // 子类方法的优先级会高于超类的同一方法
    move(distanceInMeters = 5) {
        console.log('Slithering...');
        super.move(distanceInMeters); // 调用超类的方法
    }
}

class Horse extends Animal {
    constructor(name: string) {
        super(name); // 调用超类具有相同形参的构造函数
    }

    // 子类方法的优先级会高于超类的同一方法
    move(distanceInMeters = 45) {
        console.log('Galloping...');
        super.move(distanceInMeters); // 调用超类的方法
    }
}

let sam = new Snake('Sammy the Python');
let tom: Animal = new Horse('Tommy the Palomino');

sam.move();
tom.move(34);
```

在第二个例子中我们使用 `extends` 关键字创建了 `Animal` 的两个子类： `Horse` 和 `Snake` 。

与第一个例子的不同点是，子类包含了一个构造函数 `constructor` ，这个构造函数必须调用 `super()` ，它会执行基类的构造函数。 而且，在构造函数里访问 `this` 的属性之前，我们一定要调用 `super()` 。 这个是 TypeScript 强制执行的一条重要规则。

这个例子演示了如何在子类里可以重写父类的方法。 `Snake` 类和 `Horse` 类都创建了 `move` 方法，它们重写了从 `Animal` 继承来的 `move` 方法，使得 `move` 方法根据不同的类而具有不同的功能。 注意，即使 `tom` 被声明为 `Animal` 类型，但因为它的值是 `Horse`，调用 `tom.move(34)` 时，它会调用 `Horse` 里重写的方法。

### 公共，私有与受保护的修饰符

##### 默认为：`public`

我们可以自由的访问类里定义的成员。

##### 理解 `private`

当成员被标记成 `private` 时，它就不能在声明它的类的外部访问。

```typescript
class Animal {
    private name: string;
    constructor(theName: string) {
        this.name = theName;
    }
}

new Animal('Cat').name; // 错误: 'name' 是私有的.
```

##### 理解 `protected`

`protected` 修饰符与 `private` 修饰符的行为很相似，但有一点不同， `protected` 成员在派生类中仍然可以访问。

```typescript
class Person {
    protected name: string;
    protected constructor(theName: string) {
        this.name = theName;
    }
}

// Employee 能够继承 Person
class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${
            this.department
        }.`;
    }
}

let howard = new Employee('Howard', 'Sales');
let john = new Person('John'); // 错误: 'Person' 的构造函数是被保护的。
console.log(howard.name); // 错误
```

### 静态属性

我们目前只讨论了类的实例成员，那些仅当类被实例化的时候才会被初始化的属性。 我们也可以创建类的静态成员，这些属性存在于类本身上面而不是类的实例上。 在下面这个例子里，我们使用 `static` 定义 `origin` ，因为它是所有网格都会用到的属性。每个实例想要访问这个属性的时候，都要在 `origin` 前面加上类名。 如同在实例属性上使用 `this.` 前缀来访问属性一样，这里我们使用 `Grid.` 来访问静态属性。

```typescript
class Grid {
    static origin = { x: 0, y: 0 };
    calculateDistanceFromOrigin(point: { x: number; y: number }) {
        let xDist = point.x - Grid.origin.x;
        let yDist = point.y - Grid.origin.y;
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor(public scale: number) {}
}

let grid1 = new Grid(1.0); // 1x scale
let grid2 = new Grid(5.0); // 5x scale

console.log(grid1.calculateDistanceFromOrigin({ x: 10, y: 10 }));
console.log(grid2.calculateDistanceFromOrigin({ x: 10, y: 10 }));
```

# 函数

### `this` 和箭头函数

JavaScript 里，this 的值在函数被调用的时候才会指定。 这是个既强大又灵活的特点，但是你需要花点时间弄清楚函数调用的上下文是什么。

```typescript
let deck = {
    suits: ['hearts', 'spades', 'clubs', 'diamonds'],
    cards: Array(52),
    createCardPicker: function() {
        return function() {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
        };
    }
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert('card: ' + pickedCard.card + ' of ' + pickedCard.suit);
```

可以看到 `createCardPicker` 是个函数，并且它又返回了一个函数。如果我们尝试运行这个程序，会发现它并没有弹出对话框而是报错了。 因为 `createCardPicker` 返回的函数里的 `this` 被设置成了 `window` 而不是 `deck` 对象。 因为我们只是独立的调用了 `cardPicker()` 。顶级的非方法式调用会将 `this` 视为 `window` 。（注意：在严格模式下， `this` 为 `undefined` 而不是 `window` ）。

```typescript
interface Card {
    suit: string;
    card: number;
}
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}
let deck: Deck = {
    suits: ['hearts', 'spades', 'clubs', 'diamonds'],
    cards: Array(52),
    // NOTE: The function now explicitly specifies that its callee must be of type Deck
    createCardPicker: function(this: Deck) {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
        };
    }
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert('card: ' + pickedCard.card + ' of ' + pickedCard.suit);
```
