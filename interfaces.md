# 介面 - Interfaces

TypeScript的一個核心準則是型別驗證專注在資料數值具有的'形狀'。有時這被稱作"鴨子型別(duck typing)"或"結構或子型別"(structural subtyping)"。TypeScript中介面充當了上述名稱的角色，而且在用來定義你的程式碼與你專案外的程式碼間的合約(contracts)的強而有力的方法。

## 第一個介面

要看看介面是如何運作最簡單的方法就是用一個簡單的範例開始：

```javascript
function printLabel(labelledObj: {label: string}) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

型別檢查器會檢查'printLabel'的呼叫。'printLabel'函數只有一個參數需要傳遞具有'label'的字串型別屬性的物件。我們的傳遞物件當然可能會有多個屬性，但編譯器只檢查至少具有我們需要且型別正確的內容。

我們可以在寫一次同樣的例子，這次我們使用一個介面來描述需要具有'label'的字串型別屬性：

```javascript
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

'LabelledValue'是我們在之前例子中用來描述需求的介面名稱。它仍然表示具有一個名為'label'的字串型別屬性。注意我們不需要明確地說出我們傳遞給'printLabel'的參數需要實作這個介面(在其他程式語言中可能需要)。在這裡我們只在意它的形狀。只要傳遞的物件符合我們列出的需要，就可以了。

值得說明的是型別檢查器不需要說明物件其他的屬性，只需要介面需要的屬性跟型別即可。
It's worth pointing out that the type-checker does not require that these properties come in any sort of order, only that the properties the interface requires are present and have the required type.
Optional Properties
Not all properties of an interface may be required. Some exist under certain conditions or may not be there at all. These optional properties are popular when creating patterns like "option bags" where the user passes an object to a function that only has a couple properties filled in.

Here's as example of this pattern:

interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});

Interfaces with optional properties are written similar to other interfaces, which each optional property denoted with a '?' as part of the property declaration. 

The advantage of optional properties is that you can describe these possibly available properties while still also catching properties that you know are not expected to be available. For example, had we mistyped the name of the property we passed to 'createSquare', we would get an error message letting us know:

interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.collor;  // Type-checker can catch the mistyped name here
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});  
Function Types
Interfaces are capable of describing the wide range of shapes that JavaScript objects can take. In addition to describing an object with properties, interfaces are also capable of describing function types.

To describe a function type with an interface, we give the interface a call signature. This is like a function declaration with only the parameter list and return type given.

interface SearchFunc {
  (source: string, subString: string): boolean;
}

Once defined, we can use this function type interface like we would other interfaces. Here, we show how you can create a variable of a function type and assign it a function value of the same type.

var mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  var result = source.search(subString);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}

For function types to correctly type-check, the name of the parameters do not need to match. We could have, for example, written the above example like this:

var mySearch: SearchFunc;
mySearch = function(src: string, sub: string) {
  var result = src.search(sub);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}

Function parameters are checked one at a time, with the type in each corresponding parameter position checked against each other. Here, also, the return type of our function expression is implied by the values it returns (here false and true). Had the function expression returned numbers or strings, the type-checker would have warned us that return type doesn't match the return type described in the SearchFunc interface.
Array Types
Similarly to how we can use interfaces to describe function types, we can also describe array types. Array types have an 'index' type that describes the types allowed to index the object, along with the corresponding return type for accessing the index.

interface StringArray {
  [index: number]: string;
}

var myArray: StringArray;
myArray = ["Bob", "Fred"];

There are two types of supported index types: string and number. It is possible to support both types of index, with the restriction that the type returned from the numeric index must be a subtype of the type returned from the string index.

While index signatures are a powerful way to describe the array and 'dictionary' pattern, they also enforce that all properties match their return type. In this example, the property does not match the more general index, and the type-checker gives an error:

interface Dictionary {
  [index: string]: string;
  length: number;    // error, the type of 'length' is not a subtype of the indexer
} 
Class Types
Implementing an interface
One of the most common uses of interfaces in languages like C# and Java, that of explicitly enforcing that a class meets a particular contract, is also possible in TypeScript.

interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}

You can also describe methods in an interface that are implemented in the class, as we do with 'setTime' in the below example:

interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}

Interfaces describe the public side of the class, rather than both the public and private side. This prohibits you from using them to check that a class also has particular types for the private side of the class instance.
Difference between static/instance side of class
When working with classes and interfaces, it helps to keep in mind that a class has two types: the type of the static side and the type of the instance side. You may notice that if you create an interface with a construct signature and try to create a class that implements this interface you get an error:

interface ClockInterface {
    new (hour: number, minute: number);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}

This is because when a class implements an interface, only the instance side of the class is checked. Since the constructor sits in the static side, it is not included in this check.

Instead, you would need to work with the 'static' side of the class directly. In this example, we work with the class directly:

interface ClockStatic {
    new (hour: number, minute: number);
}

class Clock  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}

var cs: ClockStatic = Clock;
var newClock = new cs(7, 30);
Extending Interfaces
Like classes, interfaces can extend each other. This handles the task of copying the members of one interface into another, allowing you more freedom in how you separate your interfaces into reusable components.

interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;

An interface can extend multiple interfaces, creating a combination of all of the interfaces.

interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
Hybrid Types
As we mentioned earlier, interfaces can describe the rich types present in real world JavaScript. Because of JavaScript's dynamic and flexible nature, you may occasionally encounter an object that works as a combination of some of the types described above. 

One such example is an object that acts as both a function and an object, with additional properties:

interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

var c: Counter;
c(10);
c.reset();
c.interval = 5.0;

When interacting with 3rd-party JavaScript, you may need to use patterns like the above to fully-describe the shape of the type.