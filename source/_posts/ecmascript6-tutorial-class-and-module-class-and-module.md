---
title: ECMAScript6教程 Class和Module（类与模块化）
date: 2015-11-07 12:26:00
updated: 2015-11-07 12:26:24
tags: 
- javascript
- hoisting
categories: 
- js

---
## Class基本语法

ES6提供了更接近传统语言的写法，引入了Class（类）这个概念，作为对象的模板。通过class关键字，可以定义类。

    //定义类
    class Point {
        constructor(x, y) {
            this.x = x;
            this.y = y;
        }
        toString() {
             return '('+this.x+', '+this.y+')';
        }
    }

上面代码定义了一个“类”，可以看到里面有一个constructor方法，这就是构造方法，而this关键字则代表实例对象。


<!--more-->


### constructor方法

constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。

## 实例对象

    var point = new Point(2, 3);

### name属性

    class Point {}
    Point.name // "Point"

### class表达式

与函数一样，Class也可以使用表达式的形式定义。

    const MyClass = class Me {
        getClassName() {
            return Me.name;
        }
    };

### Class的继承

Class之间可以通过extends关键字，实现继承。

子类会继承父类的属性和方法。

    class Point {
        constructor(x, y) {
            this.x = x;
            this.y = y;
        }
    }
     
    class ColorPoint extends Point {
        constructor(x, y, color) {
            this.color = color; // ReferenceError
            super(x, y);
            this.color = color; // 正确
        }
    }         


上面代码中，子类的constructor方法没有调用super之前，就使用this关键字，结果报错，而放在super方法之后就是正确的。

**注意**：ColorPoint继承了父类Point，但是它的构造函数必须调用super方法。

下面是生成子类实例的代码。

    let cp = new ColorPoint(25, 8, 'green');
     
    cp instanceof ColorPoint // true
    cp instanceof Point // true

class的取值函数（getter）和存值函数（setter）

在Class内部可以使用get和set关键字，对某个属性设置存值函数和取值函数。

    class MyClass {
    　　get prop() {
    　　　　return 'getter';
    　　}
    　　set prop(value) {
    　　　　document.write('setter: '+value);
    　　}
    }
     
    let inst = new MyClass();
     
    inst.prop = 123;
    // setter: 123
     
    inst.prop
    // 'getter'

### Class的Generator方法

如果某个方法之前加上星号（*），就表示该方法是一个Generator函数。

    class Foo {
        constructor(...args) {
            this.args = args;
        }
        * [Symbol.iterator]() {
            for (let arg of this.args) {
                yield arg;
            }
        }
    }
     
    for (let x of new Foo('hello', 'world')) {
        document.write(x);
    }
    // hello
    // world        

上面代码中，Foo类的Symbol.iterator方法前有一个星号，表示该方法是一个Generator函数。Symbol.iterator方法**返回一个Foo类的默认遍历器**，for...of循环会自动调用这个遍历器。

上面代码中，prop属性有对应的存值函数和取值函数，因此赋值和读取行为都被自定义了。

### Class的静态方法

类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

    class Foo {
        static classMethod() {
            return 'hello';
        }
    }
    Foo.classMethod() // 'hello'
    var foo = new Foo();
    foo.classMethod()
    // TypeError: undefined is not a function


上面代码中，Foo类的classMethod方法前有static关键字，表明该方法是一个静态方法，可以直接在Foo类上调用（Foo.classMethod()），而不是在Foo类的实例上调用。如果在实例上调用静态方法，会抛出一个错误，表示不存在该方法。

父类的静态方法，可以被子类继承。

    class Foo {
        static classMethod() {
            return 'hello';
        }
    }
    class Bar extends Foo {
    }
    Bar.classMethod(); // 'hello'

上面代码中，父类Foo有一个静态方法，子类Bar可以调用这个方法。

静态方法也是可以从super对象上调用的。

    class Foo {
        static classMethod() {
            return 'hello';
        }
    }
    class Bar extends Foo {
        static classMethod() {
            return super.classMethod() + ', too';
        }
    }
    Bar.classMethod();

### new.target属性

new是从构造函数生成实例的命令。ES6为new命令引入了一个new.target属性，（在构造函数中）返回new命令作用于的那个构造函数。如果构造函数不是通过new命令调用的，new.target会返回undefined，因此这个属性可以用来确定构造函数是怎么调用的。

    function Person(name) {
    　　if (new.target !== undefined) {
    　　　　this.name = name;
    　　} else {
    　　　　throw new Error('必须使用new生成实例');
    　　}
    }
     
    // 另一种写法
    function Person(name) {
    　　if (new.target === Person) {
    　　　　this.name = name;
    　　} else {
    　　　　throw new Error('必须使用new生成实例');
    　　}
    }
     
    var person = new Person('张三'); // 正确
    var notAPerson = Person.call(person, '张三'); // 报错

上面代码确保构造函数只能通过new命令调用。

Class内部调用new.target，返回当前Class。
子类继承父类时，new.target会返回子类。

### 修饰器

修饰器（Decorator）是一个表达式，用来修改类的行为。

修饰器函数可以接受三个参数，依次是目标函数、属性名和该属性的描述对象。后两个参数可省略。上面代码中，testable函数的参数target，就是所要修饰的对象。如果希望修饰器的行为，能够根据目标对象的不同而不同，就要在外面再封装一层函数。


    function testable(isTestable) {
    return function(target) {
    　　target.isTestable = isTestable;
    }
    }
     
    @testable(true) class MyTestableClass () {}
    document.write(MyTestableClass.isTestable) // true
     
    @testable(false) class MyClass () {}
    document.write(MyClass.isTestable) // false


如果想要为类的实例添加方法，可以在修饰器函数中，为目标类的prototype属性添加方法。

    function testable(target) {
        target.prototype.isTestable = true;
    }
    
    @testable
    class MyTestableClass () {}
     
    let obj = new MyClass();
     
    document.write(obj.isTestable) // true


### export命令

模块功能主要由两个命令构成：export和import。

export命令用于用户自定义模块，规定对外接口；
import命令用于输入其他模块提供的功能，同时创造命名空间（namespace），防止函数名冲突。

ES6允许将独立的JS文件作为模块，允许一个JavaScript脚本文件调用另一个脚本文件。

现有profile.js文件，保存了用户信息。ES6将其视为一个模块，里面用export命令对外部输出了三个变量。

    // profile.js
    var firstName = 'Michael';
    var lastName = 'Jackson';
    var year = 1958;
     
    export {firstName, lastName, year};

import命令

使用export命令定义了模块的对外接口以后，其他JS文件就可以通过import命令加载这个模块（文件）。

    // main.js
    import {firstName, lastName, year} from './profile';
     
    function sfirsetHeader(element) {
    　　element.textContent = firstName + ' ' + lastName;
    }

上面代码属于另一个文件main.js，import命令就用于加载profile.js文件，并从中输入变量。import命令接受一个对象（用大括号表示），里面指定要从其他模块导入的变量名。大括号里面的变量名，必须与被导入模块（profile.js）对外接口的名称相同。

如果想为输入的变量重新取一个名字，import语句中要使用as关键字，将输入的变量重命名。

    import { lastName as surname } from './profile';

ES6支持多重加载，即所加载的模块中又加载其他模块。

### 模块的整体输入

export命令除了输出变量，还可以输出方法或类（class）。下面是一个circle.js文件，它输出两个方法area和circumference。

    // circle.js
    export function area(radius) {
    　　return Math.PI * radius * radius;
    }
    export function circumference(radius) {
    　　return 2 * Math.PI * radius;
    }


然后，main.js输入circlek.js模块。

    // main.js
    import { area, circumference } from 'circle';
    document.write("圆面积：" + area(4));
    document.write("圆周长：" + circumference(14));

上面写法是逐一指定要输入的方法。另一种写法是整体输入。

    import * as circle from 'circle';
    document.write("圆面积：" + circle.area(4));
    document.write("圆周长：" + circle.circumference(14));

### module命令

module命令可以取代import语句，达到整体输入模块的作用。

    // main.js
    module circle from 'circle';
     
    document.write("圆面积：" + circle.area(4));
    document.write("圆周长：" + circle.circumference(14));

module命令后面跟一个变量，表示输入的模块定义在该变量上。

### export default命令

为加载模块指定默认输出，使用export default命令。

    // export-default.js
    export default function () {
    　　document.write('foo');
    }

上面代码是一个模块文件export-default.js，它的默认输出是一个函数。

其他模块加载该模块时，import命令可以为该匿名函数指定任意名字。

    // import-default.js
    import customName from './export-default';
    customName(); // 'foo'

上面代码的import命令，可以用任意名称指向export-default.js输出的方法。需要注意的是，这时import命令后面，不使用大括号。 

### 模块的继承

模块之间也可以继承。

假设有一个circleplus模块，继承了circle模块。

    // circleplus.js
     
    export * from 'circle';
    export var e = 2.71828182846;
    export default function(x) {
    　　return Math.exp(x);
    }

上面代码中的“export *”，表示输出circle模块的所有属性和方法，export default命令定义模块的默认方法。

这时，也可以将circle的属性或方法，改名后再输出。

    // circleplus.js
     
    export { area as circleArea } from 'circle';

上面代码表示，只输出circle模块的area方法，且将其改名为circleArea。

加载上面模块的写法如下。

    // main.js
     
    module math from "circleplus";
    import exp from "circleplus";
    document.write(exp(math.pi));

上面代码中的"import exp"表示，将circleplus模块的默认方法加载为exp方法。







