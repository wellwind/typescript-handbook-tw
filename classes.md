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

## 存取修飾子(Accessors)

TypeScript支援getters/setters來當作一個擷取物件成員的方法。這使你能有粒度更細的方法來控制如何存取每個物件中的成員。

讓我們使用'get'和'set'在一個簡單的類別中。我們先從一個沒有getters和setters的例子開始。

```typescript
class Employee {
    fullName: string;
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

雖然讓人們隨意設定fullName屬性很方便，但要是可以隨心所欲的的修改人們的名字也會造成麻煩。

在接下來這個版本，我們要確認user有一個祕密的通關密碼才可以修改。我們在'set'部分中替換成一個要檢查通關密碼的程式。我們加了一個對應的'get'程式來讓之前的範例一樣可以運作：

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

為了證明我們的存取修飾子現在會檢查通關密碼，我們可以修改'passcode'的內容來看看當它不符合時我們會看到一個沒有辦法更新employees內容錯誤的訊息。

注意：使用存取修飾子需要你將編譯器設定輸出為ECMAScript 5。


## 靜態屬性(Static Properties)

到目前為止，我們只討論到關於類別實體的成員，這些會在物件實例化時出現。我們也可以建立類別的靜態成員，這些靜態成員會出現在類別自己身上而不是類別的實例。接下來的例子中，我們在origin屬性加上'static'，讓他在所有Grid類別中可使用中。每個實體要存取這個資料必須透過在前面加上類別名稱。就像加上'this.'一樣，我們在存取靜態成員時在前面加上了'Grid.'。

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

### 建構子函數(Constructor functions)

當你在TypeScript中宣告一個類別時，你其實同時建立了多個宣告，第一個是類別實例的型別。

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

在此當我們說到'var greeter: Greeter'時，我們正使用Greeter作為Greeter類別的實力。這在其他物件導向語言幾乎是第二個天性。

當建構子呼叫時我們也可以建立其他的值。這是一個當我們'new'一個實體時會呼叫的函數。讓我們來看看上面例子實際上會產生什麼樣的JavaScript程式碼：

```javascript
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
'var Greeter'被指派了一個建構子函數。都我們用'new'呼叫到這個函數時，我們可以得到一個類別的實例。建構子也包含了所有的類別靜態成員。另一種思考每個類別的方法是，它們都有實例(instance)的一面與靜態(static)的一面。

接下來對之前的範例做一點修改：

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

在這個例子中，'greeter1'運作跟之前很類似，我們建立了一個'Greeter'類別的實例，然後我們使用這個物件，這跟我們之前看到的一樣。

接著，我們直接的使用類別。在這裡我們建立一個新的'greeterMaker'變數。這個變數持用類別自己，或者說它是一個建構子函數。在此我們使用'typeof Greeter'，這代表"給我Greeter類別自己的型別"，而不是它的實例型別。或更精準說"給我一個名為Greeter的符號"，它的型別是建構子函數。雖著建構子建立了Greeter類別的實例，這個型別包含了所有Greeter的靜態成員。我們接著用對'greeterMaker'使用'new'，來建立一個新的'Greeter'實例然後和之前一樣調用它。

### 將類別當作介面使用(Using a class as an interface)

如統之前章節所說，類別宣告建立兩件事情：一個類別型別的實例和一個建構子函數。因為類別也建立型別，你可以把他跟介面用在同樣的地方。

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