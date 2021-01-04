#### ES5的仿类结构
创建一个构造函数，然后在该构造函数的原型上定义方法，由此构造函数创建的实例对象都可以调用该方法。
```js
function Person(name) {
    this.name = name;
}

Person.prototype.sayName = function() {
    console.log(this.name);
}

let person1 = new Person('Jack');
person1.sayName(); // Jack
```
#### 基本的类声明
以`class`关键字开始，其后是类的名称，剩余部分类似对象的字面量形式。`class`是ES6中的一个语法糖，本质上还是基于原型的继承。
```js
class Person{
    // 构造器，作用相当于构造函数
    constructor(name){
        // 自由属性在这里面创建
        this.name = name;
    }
    sayName() {
        console.log(this.name)
    }
}

let person1 = new Person("Jack");
person1.sayName(); // Jack

console.log(typeof Person); // function
```
#### 类与构造函数之间的区别
- 类声明不会被提升，行为类似于let
- 类声明中的所有代码会自动运行在严格模式下，并且也无法退出严格模式
- 类的所有方法都是不可枚举的
- 类的所有方法内部都没有[[construct]]，使用new来调用它们会出错
- 调用类构造器时不使用new，会抛出错误
- 试图在类的方法内部重写类名，会抛出错误（外部可以）

#### 基本的类表达式

```js
//除了语法差异，功能等价于类声明
let PersonClass = class {
    constructor(name) {
        this.name = name;
    }
    sayName(){
        console.log(this.name);
    }
};
let person = new PersonClass("Nicholas");
person.sayName();   //"Nicholas"
```
#### 具名类表达式
```js
//与函数表达式类似，PersonClass2 只在类定义内部存在
let PersonClass = class PersonClass2{
    constructor(name) {
        this.name = name;
    }
    sayName(){
        console.log(this.name);
    }
};
let person = new PersonClass("Nicholas");
person.sayName();   //"Nicholas"

console.log(typeof PersonClass); //'function'
console.log(typeof PersonClass2); //'undefined'   //外部访问结果为"undefined"
```

#### 作为一级公民的类
能被当作值来使用的就称为“一级公民”，意味着它能作为参数传给函数、能作为函数返回值、能用来给变量赋值。JS的函数就是一级公民。ES6中，类也是一级公民。
```js
function createObject(classDef) {
    return new classDef();
}
//匿名类表达式作为参数
let obj = createObject(class {
    sayHi() {
        console.log("Hi!");
    }
});

obj.sayHi(); //"Hi!"
```

立即调用类构造器，以创建单例，需使用new配合类表达式来使用。
```js
let person = new class {
    constructor(name){
        this.name = name;
    }
    
    sayName(){
        console.log(this.name);
    }
}("Nicholas");

person.sayName(); //"Nicholas"
```
#### 访问器属性
getter和setter分别使用get和set关键字来创建。
```js
class CustomHTMLElement {
    constructor(element) {
        this.element = element;
    }
    
    get html(){
        return this.element.innerHTML;
    }
    
    set html(value){
        this.element.innerHTML = value;
    }
}
```

#### 需计算的成员名
语法相当于对象字面量中的需计算名称：无需使用标识符，而是用方括号来包裹一个表达式。
```js
let methodName = "sayName";

class PersonClass {
    constructor(name) {
        this.name = name;
    }
    
    [methodName]() {
        console.log(this.name);
    }
}

let me = new PersonClass("Jack");
me.sayName(); // Jack
```

#### 静态成员
使用`static`关键字标注的成员即为类的静态成员。
```js
class PersonClass
    constructor(name) {
        this.name = name;
    }
    
    sayName(){
        console.log(this.name);
    }
    
    // 静态方法，直接使用PersonClass.create()访问
    static create(name){
        return new PersonClass(name);
    }
};
let person = PersonClass.create("Nicholas");
person.sayName(); //"Nicholas"
```

#### 类的继承
使用`extends`关键字来进行类的继承，生成的类的原型会被自动调整，还能使用`super()`方法来访问基类的构造器。
```js
// 基类：被继承的类
class Rectangle {
    constructor(length, width) {
        this.length = length;
        this.width = width;
    }
    
    getArea() {
        return this.length * this.width;
    }
}

// 派生类：继承别的类的类
class Square extends Rectangle {
    constructor(length) {
        super(length, length);
    }
}

let square = new Square(3);
console.log(square.getArea()); //9
```
##### `super()`使用需要注意的点
- 只能在派生类中使用`super()`，否则会报错
- 在构造器中，必须在访问`this`之前调用`super()`，因为`super()`负责初始化`this`，否则会报错
- 唯一能避免使用`super()`的办法，是从类构造器中返回一个对象。
> 解读：使用extends实现的类的继承，要么在子类中不指定`constructor`（此时super()会被自动调用），要么就手动指明`constructor`，此时`constructor`中必须手动调用`super()`，否则会报错。若不想使用`super()`且不想报错，唯一的办法就是`constructor`中返回一个对象，但一旦这么做，虽然仍然实现了继承，但使用该派生类创建的类实例，就与该派生类断开了联系，也违背了继承的目的。示例代码下附。  

```js
class A {
    constructor(name) {
        this.name = name;
    }
    sayName() {
        console.log(this.name);
    }
    static sayHello() {
        console.log('Hello!');
    }
}

class B extends A {
    constructor (name, age) {
        return {
            name: name,
            age: age
        }
    }
}

let b1 = new B('Jack', 20);
console.log(b1 instanceof B); // false
console.log(b1 instanceof A); // false
console.log(A.isPrototypeOf(B)); // true

// 静态方法继承
B.sayHello(); // Hello
```

#### 屏蔽类方法
派生类中的方法总是会屏蔽基类的同名方法。
```js
class Rectangle {
    constructor(length, width) {
        this.length = length;
        this.width = width;
    }
    
    getArea() {
        return this.length * this.width;
    }
}

class Square extends Rectangle {
    constructor(length) {
        super(length, length);
    }
    
    // 重写并屏蔽了Reactangle中的getArea()
    getArea(){
        return  this.length * this.length; 
    }
    
    // 仍然可以通过super来调用Rectangle的getArea()
    getRectangelArea() {
        return super.getArea();
    }
}

let square = new Square(3);
console.log(square.getArea()); //9
```

#### 继承静态成员
基类中的静态成员会被继承给派生类。
```js
class A {
    constructor(name) {
        this.name = name;
    }
    sayName() {
        console.log(this.name);
    }
    static sayHello() {
        console.log('Hello!');
    }
}

class B extends A {
    constructor (name, age) {
        supe(name);
        this.age = age;
    }
}

let b1 = new B('Jack', 20);

// 静态方法继承
B.sayHello(); // Hello
```

#### 从表达式中派生类
只要一个表达式能够返回一个具有`[[Construct]`属性以及原型的函数，就可以对其使用`extends`。
```js
let SerializableMixin = {
    serialize() {
        return JSON.stringify(this);
    }
}

let AreaMixin = {
    getArea() {
        return this.length * this.width;
    }
}

function minxin(...mixins) {
    var base = function() {};
    Object.assign(base.prototype, ...mixins);
    return base;
}

class Square extends mixin(AreaMixin, SerializableMixin) {
    constructor(length) {
        super();
        this.length = this.length;
        this.width = length;
    }
}

var x = new Square(3);
console.log(x.getArea()); // 9
console.log(x.serialize()); //"{"length":3,"width":3}"
```

#### 继承内置对象
在ES5的传统继承中，`this`的值会被派生类创建，随后基类构造器才被调用，这意味着`this`一开始是派生类的实例，之后才使用了基类的附加属性对其进行了装饰。这样就出现了基于内置对象的派生类不具备内置对象部分功能的情况。  
在ES6中基于类的继承中，`this`的值会先被基类创建，随后才会被派生类的构造器所修改。这样`this`就拥有了作为基类的内置对象的所有功能。
```js
class MyArray extends Array {
    
}

let colors = new MyArray();
colors[0] = 'red';
console.log(colors.length); //1

colors.length = 0;
console.log(colors[0]); // undefined
```

#### 在类构造器中使用new.target
类构造器在调用时不能缺少`new`，因此`new.target`属性在类构造器内就始终存在，由此可以来判断类是如何被调用的。由此可以创建一个抽象基类（一种不能被实例化的类）。
```js
class Shape {
    constructor() {
        if(new.target == Shape) {
            throw new Error("This class connot be instantiated directly.")
        }
    }
}

class Rectangle extends Shape {
    constructor(length, width) {
        super();
        this.length = length;
        this.width = width;
    }
}

var x = new Shape; // 抛错，"This class connot be instantiated directly."
var y = new Rectangle(3, 4); // 正常执行
console.log(y instanceof Shape); // true
```