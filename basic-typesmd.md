# 基本型別 - Basic Types

TypeScript定義了幾個最簡單的資料單元例如：numbers, strings, structures, boolean等等。在TypeScript中支持許多在JavaScript可以看到的型別，包含了一個方便的列舉型別。

## Boolean

最近本的資料型別是簡單的true/false value，在JavaScript和TypeScript(甚至其他語言)中稱為"布林"。


```javascript
var isDone: boolean = false;
```

## Number

As in JavaScript, all numbers in TypeScript are floating point values. These floating point numbers get the type 'number'.

```javascript
var height: number = 6;
```

## String

Another fundamental part of creating programs in JavaScript for webpages and servers alike is working with textual data. As in other languages, we use the type 'string' to refer to these textual datatypes. Just like JavaScript, TypeScript also uses the double quote (") or single quote (') to surround string data.

```javascript
var name: string = "bob";
name = 'smith';
```

## Array

TypeScript, like JavaScript, allows you to work with arrays of values. Array types can be written in one of two ways. In the first, you use the type of the elements followed by '[]' to denote an array of that element type:

```javascript
var list:number[] = [1, 2, 3];
```

The second way uses a generic array type, Array<elemType>:

```javascript
var list:Array<number> = [1, 2, 3];
```

## Enum

A helpful addition to the standard set of datatypes from JavaScript is the 'enum'. Like languages like C#, an enum is a way of giving more friendly names to sets of numeric values.

```javascript
enum Color {Red, Green, Blue};
var c: Color = Color.Green;
```

By default, enums begin numbering their members starting at 0. You can change this by manually setting the value of one its members. For example, we can start the previous example at 1 instead of 0:

```javascript
enum Color {Red = 1, Green, Blue};
var c: Color = Color.Green;
```

Or, even manually set all the values in the enum:

```javascript
enum Color {Red = 1, Green = 2, Blue = 4};
var c: Color = Color.Green;
```

A handy feature of enums is that you can also go from a numeric value to the name of that value in the enum. For example, if we had the value 2 but weren't sure which that mapped to in the Color enum above, we could look up the corresponding name:

```javascript
enum Color {Red = 1, Green, Blue};
var colorName: string = Color[2];

alert(colorName);
```

## Any

We may need to describe the type of variables that we may not know when we are writing the application. These values may come from dynamic content, eg from the user or 3rd party library. In these cases, we want to opt-out of type-checking and let the values pass through compile-time checks. To do so, we label these with the 'any' type:

```javascript
var notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

The 'any' type is a powerful way to work with existing JavaScript, allowing you to gradually opt-in and opt-out of type-checking during compilation.

The 'any' type is also handy if you know some part of the type, but perhaps not all of it. For example, you may have an array but the array has a mix of different types:

```javascript
var list:any[] = [1, true, "free"];

list[1] = 100;
```

## Void

Perhaps the opposite in some ways to 'any' is 'void', the absence of having any type at all. You may commonly see this as the return type of functions that do not return a value:

```javascript
function warnUser(): void {
    alert("This is my warning message");
}
```