# 介面 - Interfaces

TypeScript的一個核心準則是型別驗證專注在資料數值具有的'形狀'。有時這被稱作"鴨子型別(duck typing)"或"結構或子型別"(structural subtyping)"。TypeScript中介面充當了上述名稱的角色，而且在用來定義你的程式碼與你專案外的程式碼間的合約(contracts)的強而有力的方法。

## 第一個介面

要看看介面是如何運作最簡單的方法就是用一個簡單的範例開始：

```typescript
function printLabel(labelledObj: {label: string}) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

型別檢查器會檢查'printLabel'的呼叫。'printLabel'函數只有一個參數需要傳遞具有'label'的字串型別屬性的物件。我們的傳遞物件當然可能會有多個屬性，但編譯器只檢查至少具有我們需要且型別正確的內容。

我們可以在寫一次同樣的例子，這次我們使用一個介面來描述需要具有'label'的字串型別屬性：

```typescript
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

值得說明的是型別檢查器不需要說明物件其他的屬性，只需要介面需要的屬性存在且型別符合即可。

## 可選擇的屬性(Optional Properties)

並不是所有介面的屬性都是必要的，在某些特定條件下是可以存在或不存在的。這些可選的屬性在建立一些模式如"選項包(option bags)"這種由使用者傳遞一組填寫好的參數物件到函數中是很受歡迎的。

以下是這種模式的例子：

```typescript
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
```

這個介面寫起來跟其他介面很類似但包含了可選擇的屬性，每個可選擇的屬性宣告並須在前面加上'?'。

可選擇屬性的優點是你可以描述這些可能可用的屬性，也能用來捕捉你不預期可用的屬性。例如：如果我們拼錯了傳給'createSquare'中的物件屬性名稱，我們會得到錯誤訊息：

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.collor;  // 型別檢查可以在這裡捕捉拼錯字的問題
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});  
```

## 函數型別(Function Types)

介面可以廣泛的描述JavaScript物件可用的範圍。除了描述物件的屬性外，介面也可以描述函數型別。

要描述介面的函數型別，我們給函數一個呼叫的簽章(signature)。就像宣告一個只有參數清單與回傳型別別的函數一樣。

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

定義完成後，我們可以像使用其他介面一樣使用這個函數型別的介面。我們在這裡顯示給你看如何建立一個函數型的變數，然後將一個同樣型別的函數指派給它。


```typescript
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
```

要對函數型別做正確的型別檢查，傳遞參數的名稱不需要一致。舉例來說，我們可以寫成像以下例子：

```typescript
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
```

函數參數會被逐個檢查，每個對應位置的參數型別是否正確。當然，函數回傳的型別也會被檢查(在這裡會檢查false跟true)。當函數內容回傳回數值或字串時，型別檢查會警告我們回傳的型別與在SearchFunc介面中描述的不同。

## 陣列型別(Array Types)

跟我們使用介面描述函數型別類似，我們也可以用來描述陣列的型別，陣列型別利用有'索引(index)'型別來描述物件中索引應該具有的型別，以及存取索引後須回傳的型別。

```typescript
interface StringArray {
  [index: number]: string;
}

var myArray: StringArray;
myArray = ["Bob", "Fred"];
```

索引型別(index types)支援兩種類型:string與number。藉由限制從數值索引回傳的型別必須是從字串索引回傳型別的子型別，要同時支援兩種也是可以的

雖然索引簽章是一種強而有力描述'字典(dictionary)'模式的方法，它們也強迫了所有的屬性必須符合回傳的型別。以下面例子來說，其中的屬性與索引型別不匹配，所以型別檢查器會產生錯誤：

```typescript
interface Dictionary {
  [index: string]: string;
  length: number;    // 錯誤, 'length'的型別不是索引子的子型別
} 
```

## 類別型別(Class Types)

### 實作介面

像C#和Java這類語言中最常使用介面的方法之一，就是明確的強迫類別的部分合約必須符合，這在TypeScript中也辦得到。

```typescript
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

你也可以在介面中描述要被類別實作的方法，例如以下範例要實做一個'setTime'：

```typescript
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
```

Interfaces describe the public side of the class, rather than both the public and private side. This prohibits you from using them to check that a class also has particular types for the private side of the class instance.
(雖然看得懂太不太知道該怎麼翻譯比較好，求救)


### 類別中static與instance的不同

當使用類別與見面時，可以幫助你心裡保持兩種類型：靜態(static)型別和實例(instance)型別。你可能注意到了如果你建立一個介面包含了建構的簽章(new)然後嘗試建立類別來時做這個介面，你會得到一個錯誤訊息：

```typescript
interface ClockInterface {
    new (hour: number, minute: number);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

這是因為當類別時做介面時，只有類別實例會被檢查，因為建構子是靜態的，因此他不會包含在這之中。

取而代之的是，當使用類別'靜態'的部分時應該直接使用，在以下例子中，我們會直接使用這個類別：

```typescript
interface ClockStatic {
    new (hour: number, minute: number);
}

class Clock  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}

var cs: ClockStatic = Clock;
var newClock = new cs(7, 30);
```

## 擴充介面(Extending Interfaces)

就像類別一樣，介面也可以彼此擴充。這可以幫你處理掉將一個介面的成員複製到另外一個介面的工作，讓你有更多的時間切割你的介面到不同可用的元件中。

```typescript
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```

An interface can extend multiple interfaces, creating a combination of all of the interfaces.

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

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

## 混合型別(Hybrid Types)

As we mentioned earlier, interfaces can describe the rich types present in real world JavaScript. Because of JavaScript's dynamic and flexible nature, you may occasionally encounter an object that works as a combination of some of the types described above. 

One such example is an object that acts as both a function and an object, with additional properties:

```typescript
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

var c: Counter;
c(10);
c.reset();
c.interval = 5.0;
```

When interacting with 3rd-party JavaScript, you may need to use patterns like the above to fully-describe the shape of the type.