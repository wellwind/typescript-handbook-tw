# 基本型別 - Basic Types

為了讓程式運作，我們需要使用一些最簡單的資料單元例如：numbers, strings, structures, boolean等等。在TypeScript中支持許多在JavaScript可以看到的型別，包含了一個方便的列舉型別來讓其更完整。

## Boolean

最基本的資料型別是簡單的true/false value，在JavaScript和TypeScript(甚至其他語言)中稱為'boolea'值。


```javascript
var isDone: boolean = false;
```

## Number

如同在JavaScript中一樣，所有在TypeScript的數值資料皆為浮點數，這些浮點數的型別為'number'。

```javascript
var height: number = 6;
```

## String

另一個在網頁或伺服計端的JavaScriptt程式運作的基本是文字資料，如同其他語言一樣，我們使用'string'代表這些文字的資料型別。和JavaScript一樣，TypeScript也使用雙引號(")或單引號(')來將字串資料包住。

```javascript
var name: string = "bob";
name = 'smith';
```

## Array

TypeScript與JavaScript一樣，允許你操作數值陣列，陣列型別可以用兩種方式撰寫。第一種方式，你可以在宣告的型別後面加上'[]'宣告的型別為陣列。

```javascript
var list:number[] = [1, 2, 3];
```
第二種方式使用泛型的陣列型別, Array&lt;elemType&gt;

```javascript
var list:Array<number> = [1, 2, 3];
```

## Enum

在JavaScript中有一個對於標準的資料型別集合很有幫助的額外補充為'enum'。像C#這類語言一樣，列舉型別是一個讓一組數值有更友善的名稱表示的方法。


```javascript
enum Color {Red, Green, Blue};
var c: Color = Color.Green;
```

列許型別預設從0開始給予其中每個成員編號。你可以藉由設定其中一個數成員的數值來改變它。例如：我們可以將讓上一個例子由1開始：

```javascript
enum Color {Red = 1, Green, Blue};
var c: Color = Color.Green;
```

甚至我們可以為每個列舉成員設定它代表的值：e enum:

```javascript
enum Color {Red = 1, Green = 2, Blue = 4};
var c: Color = Color.Green;
```

你也可以從列舉成員的對應數值來找到它的名稱，例如我們知道一個列舉成員的值為2但不知道它對應到上面宣告的Color列舉中的哪個成員，我們可以找出它對應的名稱：

```javascript
enum Color {Red = 1, Green, Blue};
var colorName: string = Color[2];

alert(colorName);
```

## Any

當在撰寫程式時我們可能還不知道要如何描述一個變數的資料型別。這些值可能是來自動態的內容例如使用者或第三方的類別庫。這種情況下為了讓編譯檢查可以通過，我們會想要將它移除型別檢查的範圍。我們可使用'any'型別來達到此目的：

```javascript
var notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```
在同時使用現有的JavaScript時'any'型別是非常有利的做法，可以讓你在編譯時逐步的將型別加入檢查。

如果在你只知道一部份的型別，但不是全部知道時'any'型別也非常好用，例如，你有一個混合了不同型別的陣列時：

```javascript
var list:any[] = [1, true, "free"];

list[1] = 100;
```

## Void

'void'與'any'可能是剛好相反的作法，代表根本沒有任的型別，你通常可以在不會回傳任何型別的function中看到：


```javascript
function warnUser(): void {
    alert("This is my warning message");
}
```