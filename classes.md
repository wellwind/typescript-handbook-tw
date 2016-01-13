# 類別 - Classes

傳統的JavaScript專注在函數和以雛型為基礎(prototype-based)的架構上當作建立可重用性組件的基本概念，但這可能會讓一些型慣用物件導向開發的程式設計師感覺到有些尷尬，其中類別繼承的功能和目標是來自於建構這些類別。從ECMAScript 6開始，之後的JavaScript版本程式設計人員可以使用物件導向的方式來建立應用程式。而在TypeScript，我們現在就可以使用這些技術，並將程式碼編譯成主流的瀏覽器及平台相容的版本，而不用等待下一個JavaScript版本出現。

## 類別(Classes)

首先先來看個簡單的類別範例：

```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

var greeter = new Greeter("world");
```

如果你用過C#或Java，上面的表達方式看起來會很熟悉。我們軒高了一個新的'Greeter'類別。這個類別包含三個成員，有一個'greeting'屬性，一個建構子和一個'greet'方法。

你會發現在類別中當我們要參考類別成員時我們會在前面加上'this.'。這代表了要存取其中的成員。

最後一行我們使用'new'建立了一個Greeter類別的實例。這會呼叫我們之前定義好的建構子，建立一個新的具有Greeter外型的物件，然後執行建構子來初始化它。

## 繼承(Inheritance)

在TypeScript中我們可以使用物件導向的模式。當然，其中一個以類別為基礎(class-based)的程式設計基本模式是我們可以利用繼承來擴充現有類別並產生一個新的類別。

讓我們來看以下這個例子：

```typescript
class Animal {
    name:string;
    constructor(theName: string) { this.name = theName; }
    move(meters: number = 0) {
        alert(this.name + " moved " + meters + "m.");
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(meters = 5) {
        alert("Slithering...");
        super.move(meters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(meters = 45) {
        alert("Galloping...");
        super.move(meters);
    }
}

var sam = new Snake("Sammy the Python");
var tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

這個例子涵蓋了許多TypeScript中繼承的特性，我們在這裡使用'extends'關鍵字來建立一個子類別，你可以看到'Hourse'和'Snake'子類別都是以'Animal'類別為基礎並獲得'Animal'的特徵。

這個例子也展示了我們可以在子類別中覆蓋基礎類別具有的方法。'Snake'和'Hourse'建立了一個'move'方法來故蓋掉原有了'Animal'的'move'方法，讓他在各自的類別中有專門的功能。

## 私有/公用修飾子(Private/Public modifiers)

### 預設為公用(Public)

你可能注意到了上面的範例中我們並沒有使用'public'來讓人和類別成員顯示。輛C#這類的語言需要明確的標示'public'來讓成員顯示。而在TypeScript中，所有類別成員預設都是公開的。

你依然可以將成員標示為'private'，藉此控制那些成員在類別外是可見的。我們可以將之前的'Animal'類別改寫成：

```typescript
class Animal {
    private name:string;
    constructor(theName: string) { this.name = theName; }
    move(meters: number) {
        alert(this.name + " moved " + meters + "m.");
    }
}
```

### 深入了解private

TypeScript是一種結構式的型別系統。當我們比較兩個不同型別的成員時，無論他們來自哪裡，只要每個型別中的成員是相容的，那麼我們就會說這兩個型別是相容的。

當比較到具有'private'的成員時，我們會用不同的方式處理。對於兩個型別是否相容，如果其中一個型別具有私有成員，那麼另外一個型別一定也會有一個私有成員是來自源頭的宣告。

來看個例子比較容易看出實際上的情況：

```typescript
class Animal {
    private name:string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
	constructor() { super("Rhino"); }
}

class Employee {
    private name:string;
    constructor(theName: string) { this.name = theName; }	
}

var animal = new Animal("Goat");
var rhino = new Rhino();
var employee = new Employee("Bob");

animal = rhino;
animal = employee; //錯誤: Animal和Employee不相容
```

這個例子中。我們有'Animal'和'Rhino'，'Rhino'是'Animal'的子類別。我們也有另外一個新的'Employee'類別看起來跟'Animal'的形狀一樣。我們對這些類別建立實例並嘗試指派給彼此來看看會發生什麼事。因為'Animal'和'Rhino'共享同樣的形狀中相同宣告的私有成員為'Animal'中的'private name: string'，所以它們是相容的。當我們嘗試將'Employee'指派給'Animal'時會發生型別不相容的錯誤。儘管'Employee'也有一個名為'name'的私有成員，但它跟在'Animal'中建立的是不同的。

### 參數屬性(Parameter properties)

藉由參數屬性，'public'和'private'關鍵字也可以用來作為建立和初始化類別成員的方法。這些屬性讓你可以一步建立和初始化成員。下面是之前例子的一個修改版本。注意我們移除'theName'然後在建構中使用'private name: string'參數來建立並初始化'name'成員。

```typescript
class Animal {
    constructor(private name: string) { }
    move(meters: number) {
        alert(this.name + " moved " + meters + "m.");
    }
}
```

如此使用'private'會建立一個並處史話一個私有的成員, 使用'public'也有相似得效果。

## 存取子(Accessors)
TypeScript supports getters/setters as a way of intercepting accesses to a member of an object. This gives you a way of having finer-grained control over how a member is accessed on each object.

Let's convert a simple class to use 'get' and 'set'. First, let's start with an example without getters and setters.

class Employee {
    fullName: string;
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}

While allowing people to randomly set fullName directly is pretty handy, this might get us in trouble if we people can change names on a whim. 

In this version, we check to make sure the user has a secret passcode available before we allow them to modify the employee. We do this by replacing the direct access to fullName with a 'set' that will check the passcode. We add a corresponding 'get' to allow the previous example to continue to work seamlessly.

```typescript
var passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }
	
    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            alert("Error: Unauthorized update of employee!");
        }
    }
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

To prove to ourselves that our accessor is now checking the passcode, we can modify the passcode and see that when it doesn't match we instead get the alert box warning us we don't have access to update the employee.

Note: Accessors require you to set the compiler to output ECMAScript 5.
Static Properties
Up to this point, we've only talked about the instance members of the class, those that show up on the object when its instantiated. We can also create static members of a class, those that are visible on the class itself rather than on the instances. In this example, we use 'static' on the origin, as it's a general value for all grids. Each instance accesses this value through prepending the name of the class. Similarly to prepending 'this.' in front of instance accesses, here we prepend 'Grid.' in front of static accesses.
```typescript
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        var xDist = (point.x - Grid.origin.x);
        var yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

var grid1 = new Grid(1.0);  // 1x scale
var grid2 = new Grid(5.0);  // 5x scale

alert(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
alert(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```

## Advanced Techniques

### Constructor functions

When you declare a class in TypeScript, you are actually creating multiple declarations at the same time. The first is the type of the instance of the class.

```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

var greeter: Greeter;
greeter = new Greeter("world");
alert(greeter.greet());
```

Here, when we say 'var greeter: Greeter', we're using Greeter as the type of instances of the class Greeter. This is almost second nature to programmers from other object-oriented languages. 

We're also creating another value that we call the constructor function. This is the function that is called when we 'new' up instances of the class. To see what this looks like in practice, let's take a look at the JavaScript created by the above example:

```typescript
var Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();

var greeter;
greeter = new Greeter("world");
alert(greeter.greet());
```

Here, 'var Greeter' is going to be assigned the constructor function. When we call 'new' and run this function, we get an instance of the class. The constructor function also contains all of the static members of the class. Another way to think of each class is that there is an instance side and a static side.

Let's modify the example a bit to show this difference:

```typescript
class Greeter {
    static standardGreeting = "Hello, there";
    greeting: string;
    greet() {
        if (this.greeting) {
            return "Hello, " + this.greeting;
        }
        else {
            return Greeter.standardGreeting;
        }
    }
}

var greeter1: Greeter;
greeter1 = new Greeter();
alert(greeter1.greet());

var greeterMaker: typeof Greeter = Greeter;
greeterMaker.standardGreeting = "Hey there!";
var greeter2:Greeter = new greeterMaker();
alert(greeter2.greet());
```

In this example, 'greeter1' works similarly to before. We instantiate the 'Greeter' class, and use this object. This we have seen before.

Next, we then use the class directly. Here we create a new variable called 'greeterMaker'. This variable will hold the class itself, or said another way its constructor function. Here we use 'typeof Greeter', that is "give me the type of the Greeter class itself" rather than the instance type. Or, more precisely, "give me the type of the symbol called Greeter", which is the type of the constructor function. This type will contain all of the static members of Greeter along with the constructor that creates instances of the Greeter class. We show this by using 'new' on 'greeterMaker', creating new instances of 'Greeter' and invoking them as before.
Using a class as an interface
As we said in the previous section, a class declaration creates two things: a type representing instances of the class and a constructor function. Because classes create types, you can use them in the same places you would be able to use interfaces.

```typescript
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

var point3d: Point3d = {x: 1, y: 2, z: 3};
```