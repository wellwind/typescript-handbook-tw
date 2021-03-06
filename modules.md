# 模組 - Modules

接下來我們要看利用TypeScript的模組功能組織程式碼的一些方法。將會涵蓋內部跟外部的模組，然後我們將會探討當如何使用及何時適用。我們也會提到一些關於使用外部模組的進階議題，和使用TypeScript模組時常見的陷阱。

### 第一步

我們以接下來的程式作為整個章節的開始。我們已經寫了一小組簡單的字串驗證器程式，就像你可能會在網頁上檢查使用者在表單中輸入的內容格式，或是檢查外部檔案類型一樣。

#### 單一檔案中的驗證器

```typescript
interface StringValidator {
    isAcceptable(s: string): boolean;
}

var lettersRegexp = /^[A-Za-z]+$/;
var numberRegexp = /^[0-9]+$/;

class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}

class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: StringValidator; } = {};
validators['ZIP code'] = new ZipCodeValidator();
validators['Letters only'] = new LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

### 加入模組化

當我們有更多的驗證器時，我們需要更有規劃的組織來追蹤型別而又不用擔心與其他物件的命名衝突。比起將一大堆的命名放在全域的命名空間(global namespace)中，我們可以把這些物件包裝進一個模組中。

在接下來的例子我們相驗證器相關的型別放到一個Validation模組中。因為我們希望相關的介面跟類別在模組外是可用的，我以我們替它們加上'export'。相反的letterRegexp和numberRegexp是實作的細節，所以我們要讓它無法讓模組外的程式碼看得到。在檔案下面的測試程式碼中，當我們在模組外要使用這些型別時我們需要修飾型別的名稱，例如Validation.LettersOnlyValidator。

#### 模組化後的驗證器

```typescript
module Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }

    var lettersRegexp = /^[A-Za-z]+$/;
    var numberRegexp = /^[0-9]+$/;

    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }

    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: Validation.StringValidator; } = {};
validators['ZIP code'] = new Validation.ZipCodeValidator();
validators['Letters only'] = new Validation.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

## 分割到多個檔案

當我們的程式越來越大時，我們需要將程式碼分割到不同的檔案以便於維護。

接下來我們將Validation模組分割到多個檔案中。儘管檔案被分開了，它們還是可以共用同一個模組，就像他們被定義在同一個地方一樣。因為他們節由檔案相依，我們要加上reference標籤告訴編譯器檔案之間的關聯。我們的測試程式碼在這裡不做修改。

### Multi-file internal modules

Validation.ts
```typescript
module Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
}
```
LettersOnlyValidator.ts
```typescript
/// <reference path="Validation.ts" />
module Validation {
    var lettersRegexp = /^[A-Za-z]+$/;
    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }
}
```

ZipCodeValidator.ts
```typescript
/// <reference path="Validation.ts" />
module Validation {
    var numberRegexp = /^[0-9]+$/;
    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}
```

Test.ts
```typescript
/// <reference path="Validation.ts" />
/// <reference path="LettersOnlyValidator.ts" />
/// <reference path="ZipCodeValidator.ts" />

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: Validation.StringValidator; } = {};
validators['ZIP code'] = new Validation.ZipCodeValidator();
validators['Letters only'] = new Validation.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

當在些檔案被包含進來後，我們必須要確認所有程式碼都被編譯進來，有兩中方式可以做到。

第一個，我們可以藉由 --out 參數來將輸入的檔案編譯一個JavaScript之中。

```
tsc --out sample.js Test.ts
```

編譯器會自動依照檔案中reference標籤來排序到輸出檔案中，你也可以獨立指定每個檔案。

```
tsc --out sample.js Validation.ts LettersOnlyValidator.ts ZipCodeValidator.ts Test.ts
```

除此之外，我們可以將每個檔案都利用預設的編譯方式輸出成各自的JavaScript檔案，當這些檔案被產生後，我們需要在網頁中使用&lt;script&gt;標籤來依序讀取檔案，例如：, for example:

MyTestPage.html (片段程式)

```html
    <script src="Validation.js" type="text/javascript" />
    <script src="LettersOnlyValidator.js" type="text/javascript" />
    <script src="ZipCodeValidator.js" type="text/javascript" />
    <script src="Test.js" type="text/javascript" />
```

## 往外部移動

TypeScript也有外部模組的概念。外部模組被用在兩種狀況：node.js和require.js。不使用node.js或require.js的程式不會需要用到外部模組，且最好的組織方法就是使用之前說明的內部模組概念。

In external modules, relationships between files are specified in terms of imports and exports at the file level. In TypeScript, any file containing a top-level import or export is considered an external module.

Below, we have converted the previous example to use external modules. Notice that we no longer use the module keyword – the files themselves constitute a module and are identified by their filenames.

The reference tags have been replaced with import statements that specify the dependencies between modules. The import statement has two parts: the name that the module will be known by in this file, and the require keyword that specifies the path to the required module:

```typescript
import someMod = require('someModule');
```

We specify which objects are visible outside the module by using the export keyword on a top-level declaration, similarly to how export defined the public surface area of an internal module.

To compile, we must specify a module target on the command line. For node.js, use --module commonjs; for require.js, use --module amd. For example:
tsc --module commonjs Test.ts

When compiled, each external module will become a separate .js file. Similar to reference tags, the compiler will follow import statements to compile dependent files.
Validation.ts
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
LettersOnlyValidator.ts
```typescript
import validation = require('./Validation');
var lettersRegexp = /^[A-Za-z]+$/;
export class LettersOnlyValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}
```

ZipCodeValidator.ts
```typescript
import validation = require('./Validation');
var numberRegexp = /^[0-9]+$/;
export class ZipCodeValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
```

Test.ts
```typescript
import validation = require('./Validation');
import zip = require('./ZipCodeValidator');
import letters = require('./LettersOnlyValidator');

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: validation.StringValidator; } = {};
validators['ZIP code'] = new zip.ZipCodeValidator();
validators['Letters only'] = new letters.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```
Code Generation for External Modules
Depending on the module target specified during compilation, the compiler will generate appropriate code for either node.js (commonjs) or require.js (AMD) module-loading systems. For more information on what the define and require calls in the generated code do, consult the documentation for each module loader.

This simple example shows how the names used during importing and exporting get translated into the module loading code.
SimpleModule.ts
import m = require('mod');
export var t = m.something + 1;
AMD / RequireJS SimpleModule.js:
define(["require", "exports", 'mod'], function(require, exports, m) {
    exports.t = m.something + 1;
});
CommonJS / Node SimpleModule.js:
var m = require('mod');
exports.t = m.something + 1;

## Export =

In the previous example, when we consumed each validator, each module only exported one value. In cases like this, it's cumbersome to work with these symbols through their qualified name when a single identifier would do just as well.

The export = syntax specifies a single object that is exported from the module. This can be a class, interface, module, function, or enum. When imported, the exported symbol is consumed directly and is not qualified by any name.

Below, we've simplified the Validator implementations to only export a single object from each module using the export = syntax. This simplifies the consumption code – instead of referring to 'zip.ZipCodeValidator', we can simply refer to 'zipValidator'.
Validation.ts
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
LettersOnlyValidator.ts
import validation = require('./Validation');
var lettersRegexp = /^[A-Za-z]+$/;
class LettersOnlyValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}
export = LettersOnlyValidator;
ZipCodeValidator.ts
import validation = require('./Validation');
var numberRegexp = /^[0-9]+$/;
class ZipCodeValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;
Test.ts
import validation = require('./Validation');
import zipValidator = require('./ZipCodeValidator');
import lettersValidator = require('./LettersOnlyValidator');

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: validation.StringValidator; } = {};
validators['ZIP code'] = new zipValidator();
validators['Letters only'] = new lettersValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
Alias
Another way that you can simplify working with either kind of module is to use import q = x.y.z to create shorter names for commonly-used objects. Not to be confused with the import x = require('name') syntax used to load external modules, this syntax simply creates an alias for the specified symbol. You can use these sorts of imports (commonly referred to as aliases) for any kind of identifier, including objects created from external module imports.
Basic Aliasing
module Shapes {
    export module Polygons {
        export class Triangle { }
        export class Square { }
    }
}

import polygons = Shapes.Polygons;
var sq = new polygons.Square(); // Same as 'new Shapes.Polygons.Square()'

Notice that we don't use the require keyword; instead we assign directly from the qualified name of the symbol we're importing. This is similar to using var, but also works on the type and namespace meanings of the imported symbol. Importantly, for values, import is a distinct reference from the original symbol, so changes to an aliased var will not be reflected in the original variable.
Optional Module Loading and Other Advanced Loading Scenarios
In some cases, you may want to only load a module under some conditions. In TypeScript, we can use the pattern shown below to implement this and other advanced loading scenarios to directly invoke the module loaders without losing type safety.

The compiler detects whether each module is used in the emitted JavaScript. For modules that are only used as part of the type system, no require calls are emitted. This culling of unused references is a good performance optimization, and also allows for optional loading of those modules.

The core idea of the pattern is that the import id = require('...') statement gives us access to the types exposed by the external module. The module loader is invoked (through require) dynamically, as shown in the if blocks below. This leverages the reference-culling optimization so that the module is only loaded when needed. For this pattern to work, it's important that the symbol defined via import is only used in type positions (i.e. never in a position that would be emitted into the JavaScript).

To maintain type safety, we can use the typeof keyword. The typeof keyword, when used in a type position, produces the type of a value, in this case the type of the external module.
Dynamic Module Loading in node.js
declare var require;
import Zip = require('./ZipCodeValidator');
if (needZipValidation) {
    var x: typeof Zip = require('./ZipCodeValidator');
    if (x.isAcceptable('.....')) { /* ... */ }
}
Sample: Dynamic Module Loading in require.js
declare var require;
import Zip = require('./ZipCodeValidator');
if (needZipValidation) {
    require(['./ZipCodeValidator'], (x: typeof Zip) => {
        if (x.isAcceptable('...')) { /* ... */ }
    });
}
Working with Other JavaScript Libraries
To describe the shape of libraries not written in TypeScript, we need to declare the API that the library exposes. Because most JavaScript libraries expose only a few top-level objects, modules are a good way to represent them. We call declarations that don't define an implementation "ambient". Typically these are defined in .d.ts files. If you're familiar with C/C++, you can think of these as .h files or 'extern'. Let's look at a few examples with both internal and external examples.
Ambient Internal Modules
The popular library D3 defines its functionality in a global object called 'D3'. Because this library is loaded through a script tag (instead of a module loader), its declaration uses internal modules to define its shape. For the TypeScript compiler to see this shape, we use an ambient internal module declaration. For example:
D3.d.ts (simplified excerpt)
declare module D3 {
    export interface Selectors {
        select: {
            (selector: string): Selection;
            (element: EventTarget): Selection;
        };
    }

    export interface Event {
        x: number;
        y: number;
    }

    export interface Base extends Selectors {
        event: Event;
    }
}

declare var d3: D3.Base;
Ambient External Modules
In node.js, most tasks are accomplished by loading one or more modules. We could define each module in its own .d.ts file with top-level export declarations, but it's more convenient to write them as one larger .d.ts file. To do so, we use the quoted name of the module, which will be available to a later import. For example:
node.d.ts (simplified excerpt)
declare module "url" {
    export interface Url {
        protocol?: string;
        hostname?: string;
        pathname?: string;
    }

    export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
}

declare module "path" {
    export function normalize(p: string): string;
    export function join(...paths: any[]): string;
    export var sep: string;
}

Now we can /// <reference> node.d.ts and then load the modules using e.g. import url = require('url');.

///<reference path="node.d.ts"/>
import url = require("url");
var myUrl = url.parse("http://www.typescriptlang.org");

Pitfalls of Modules
In this section we'll describe various common pitfalls in using internal and external modules, and how to avoid them.
/// <reference> to an external module
A common mistake is to try to use the /// <reference> syntax to refer to an external module file, rather than using import. To understand the distinction, we first need to understand the three ways that the compiler can locate the type information for an external module.

The first is by finding a .ts file named by an import x = require(...); declaration. That file should be an implementation file with top-level import or export declarations.

The second is by finding a .d.ts file, similar to above, except that instead of being an implementation file, it's a declaration file (also with top-level import or export declarations).

The final way is by seeing an "ambient external module declaration", where we 'declare' a module with a matching quoted name.
myModules.d.ts
// In a .d.ts file or .ts file that is not an external module:
declare module "SomeModule" {
    export function fn(): string;
}
myOtherModule.ts
/// <reference path="myModules.d.ts" />
import m = require("SomeModule");

The reference tag here allows us to locate the declaration file that contains the declaration for the ambient external module. This is how the node.d.ts file that several of the TypeScript samples use is consumed, for example.
Needless Namespacing
If you're converting a program from internal modules to external modules, it can be easy to end up with a file that looks like this:
shapes.ts
export module Shapes {
    export class Triangle { /* ... */ }
    export class Square { /* ... */ }
}

The top-level module here Shapes wraps up Triangle and Square for no reason. This is confusing and annoying for consumers of your module:
shapeConsumer.ts
import shapes = require('./shapes');
var t = new shapes.Shapes.Triangle(); // shapes.Shapes?

A key feature of external modules in TypeScript is that two different external modules will never contribute names to the same scope. Because the consumer of an external module decides what name to assign it, there's no need to proactively wrap up the exported symbols in a namespace.

To reiterate why you shouldn't try to namespace your external module contents, the general idea of namespacing is to provide logical grouping of constructs and to prevent name collisions. Because the external module file itself is already a logical grouping, and its top-level name is defined by the code that imports it, it's unnecessary to use an additional module layer for exported objects.

Revised Example:
shapes.ts
export class Triangle { /* ... */ }
export class Square { /* ... */ }
shapeConsumer.ts
import shapes = require('./shapes');
var t = new shapes.Triangle(); 
Trade-offs for External Modules
Just as there is a one-to-one correspondence between JS files and modules, TypeScript has a one-to-one correspondence between external module source files and their emitted JS files. One effect of this is that it's not possible to use the --out compiler switch to concatenate multiple external module source files into a single JavaScript file.