---
title: 与typescript相识相爱
date: 2019-06-30 14:50:47
tags: ["typescript"]
categories: JavaScript
---

# 前言

[参考TypeScript入门教程](https://ts.xcatliu.com/)

之前一直不了解TypeScript，现在用到了，就来study 一波，结合TypeScript入门教程一书，谈及自己一步步的学习历程。

# 简介 

学习一个东西肯定得先大概了解一下这是个什么东西吧，它是用来干嘛的，怎么安装它，先弄出个hello world.

## what is it?

大家以及官网说法：TypeScript 是 javscript的超集。对不起乡下来的，听不懂啥叫超集，根据个人理解，typescript是把javascript写的更加严谨，更加规范了，先写完ts(下面对typescript简称)，然后再编译成js,在编译过程中就把不规范的地方进行报错，提示。尽管报错但是还是会编译出来js的。

## how to run it?

如上所述，那么问题来了，怎么编译呢？ 

全局安装  `npm install -g typescript `

编译一个TypeScript文件： `tsc hello.ts`

## hello world

新建文件 hello.ts:
```Typescript
function sayHello(person: string) {
    return 'Hello, ' + person;
}

let user = 'Tom';
console.log(sayHello(user));
```
然后执行 `tsc hello.ts`
这时候会生成一个编译好的文件 hello.js

```js
function sayHello(person) {
    return 'Hello, ' + person;
}
var user = 'Tom';
console.log(sayHello(user));
```

**知识点**:ts中使用 `：`来制定变量的类型，前后有没有空格无所谓。

如果给person 传入的不是字符串就会报错，但是还会生成编译结果

**知识点**:如果要在报错的时候终止js文件的生成，可以再`tsconfig.json`中配置`onEmitOnError`即可。关于tsconfig.json,参考[官方手册](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/tsconfig.json.html) 

# 基础干货

接下来是正儿八经的干货了，把typescript大体过一遍

## 原始数据类型

众所周知 js的数据类型 可以分为两大类：原始数据类型和对象类型

我们先讨论原始数据类型： 布尔值、数值、字符串、null、undefined 以及 ES6 中的新类型 Symbol

### 布尔值

- ts中使用 `boolean`定义布尔值类型
- **注意使用构造函数Boolean创造的对象不是布尔值**
```js
let createdByNewBoolean: boolean = new Boolean(1);
// 编译的时候报错  
```
- 直接调用，而不用new，是可以返回一个boolean类型的：
```
let createdByBoolean: boolean = Boolean(1);
```

依次类推 new出来的 都是不能通过编译的

### 数值

使用 `number`定义数值型

### 字符串

使用 `string`定义字符串

### 空值

这个东西注意一下，js并没有这个概念，而在ts中，可以用void表示没有任何返回值的函数

```
function alertName(): void {
    alert('My name is Tom');
}
```

### Null和Undefined

```
let u: undefined = undefined;
let n: null = null;
```

undefined和null 其他类型的子类型。说白了就是 一个变量我们不是规定它应该是字符串或者其他类型，但是的值也是可以是undefined或者null。

## 任意值

这个时候你是不是有个问题，万一我们不知道 一个变量是啥类型怎么，或者一个值可以有很多类型咋弄？ 别慌ts中有个any类型。也就是可以用它来表示任意类型

- 任意类型可以被赋予任何类型的值
- 声明一个变量为任意值，得到的也是任意值
- 未声明的变量自动被识别为任意类型
- 任意类型可以随便搞

## 类型推论

注意下面两种情况，一种声明完了直接赋值，一种先声明再赋值


```
//第一种
let myFavoriteNumber = 'seven';
myFavoriteNumber = 7;

```
```
//第二种
let myFavoriteNumber;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

> 第一种编译报错，ts它根据你的赋值，推测出它的类型是字符串，而第二种则因为没有规定类型被认为是any类型，所以可以随便搞

## 联合类型

不知道为啥起这个名字，说成白话文，就是类型你可以规定多个，不用只规定一个，这样赋值的时候再你规定的那几种里面，只要满足一个就行

- 联合类型用`|`分隔每个类型
- 访问联合类型的属性或方法
```
function getLength(something: string | number): number {
    return something.length;
}
```
> 这里参数可能是字符串也可能是数字，但是lenth不是 string和number的公共属性，所以编译的时候就会报错
> 访问string和number公共的属性是没有问题的
> 注意一下，当变量被赋值的时候 ts会推断它的类型
```
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
console.log(myFavoriteNumber.length); // 5
myFavoriteNumber = 7;
console.log(myFavoriteNumber.length); // 编译时报错
```
## 对象的类型--接口

在js中 我们一个对象可以有很多属性，这些属性的值，可以有的是字符串类型，有的是数字类型，最开始我们说过ts就是把js写的更加严谨，所以ts就用接口（Interfaces）来定义对象的类型。

> 接口是对行为的一种抽象，他不去管具体怎么实现，就只是描述干嘛了，大体是啥东西

简单的例子

```
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```
**接口一般首字母大写**
- 注意接口是用`;`
- 这个时候注意了，我们接口定义了name,age那么接下来的变量就必须有这两个属性，多一个不行，少一个也不行，官方说就是赋值的时候，变量的形状必须和接口的形状保持一致。
- 那么问题来了 ，我们想要不确定有些属性有还是没有怎么办，下面可选属性登场

### 可选属性

```
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom'
};
```
打个`?`表示这个属性可有可无

### 任意属性


```
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```
这里有个坑爹的地方，**一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集**
上面代码中，propName（属性名）的类型是字符串类型，而该属性的值的类型是任何类型，any类型是基础类型的集合。

```
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};
// 编译 报错


正确写法

interface Person {
    name: string;
    age?: number;
    [propName: string]: string | number;
}

```

### 只读属性

有时候我们希望对象中的一些字段只能在创建的时候被赋值，那么可以用 `readonly` 定义只读属性：
```
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}
```

这里需要注意一点，如果出现只读属性，那么我们对对象赋值的时候，只读属性必须第一次就被赋值，否则会出现编译错误

## 数组的类型

说完了 对象接下来轮到数组了

### 类型[] 来标示数组类型

例如：
```
let fibonacci: number[] = [1, 1, 2, 3, 5];
```
这是个数字类型组成的数组，里面不允许其他类型的存在，如果存在其他类型，则编译报错

数组的一些方法push,pop等 也会受到限制，只能添加数字类型的值

### 数组的泛型

也可以使用数组泛型（Array Generic） `Array<elemType>`来表示数组：
```
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```
关于泛型，下面介绍

### 用接口表示数组

```
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```
感觉这里就跟js扯上关系了，我们都知道 js中万物皆是对象，数组key就是数组的索引index，类型是number

### any在数组中的应用
用来表示数组中啥类型的都有，东北菜大锅炖

```
let list: any[] = ['Xcat Liu', 25, { website: 'http://xcatliu.com' }];
```

### 类数组

注意 类数组不是数组类型 比如`arguments`

常见的类数组都有自己的接口定义，如IArguments, NodeList, HTMLCollection 等

这些内置对象我们后面详细唠

## 函数的类型 
在JavaScript 中，有两种常见的定义函数的方式——函数声明（Function Declaration）和函数表达式，两种方式的类型规定是不同的
规定时我们需要考虑限制输入的类型和输出的类型都需要进行规定。

### 函数声明的类型规定

```
function sum(x: number, y: number): number {
    return x + y;
}
```
**注意输入多余的（或者少于要求的）参数，是不被允许的**

### 函数表达式的类型规定

```
let mySum = function (x: number, y: number): number {
    return x + y;
};
```
**注意上面的方法依旧是函数声明类型的规定方式**

正确的做法是

```
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
};
```
**注意这里的`=>`并不是ES6的箭头函数，这里`=>`左边指的是输入类型，右边指的输出型**

### 用接口定义函数的形状

```
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```

### 可选参数

上面肯定你会有疑问，上面的参数都是固定的，但是实际上我们写的函数往往参数都不是固定的数量的输入，那么我们该如何处理呢？

还记得前面的`？`吗，它就用来表示不确定，即可选参数
```
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```
**需要注意的地方是，可选参数必须接在必须参数后面，也就是可选参数后面不能再有必须参数**

### 参数默认值

在ES6中，我们允许给函数的参数添加默认值，TypeScript会将添加默认值的参数识别为可选参数，这也说的过去，确实调用的时候参数也是可选的。

但是这个时候，**可选参数就可以不必要一定跟在必选参数的后面了**

### 剩余参数

ES6中，可以用`...`接收剩余参数，剩余参数的本质也就是把那些参数合并成一个数组嘛，这样我们就可以数组的类型来定义它们。
```
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}

let a = [];
push(a, 1, 2, 3);
```

### 重载

重载允许一个函数接收不同数量或类型的参数时，作出不同的处理。

Typescript入门教程讲的不错 参考：

比如，我们需要实现一个函数 reverse，输入数字 123 的时候，输出反转的数字 321，输入字符串 'hello' 的时候，输出反转的字符串 'olleh'。

利用联合类型，我们可以这么实现：

```
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
} 
```
然而这样有一个缺点，就是不能够精确的表达，输入为数字的时候，输出也应该为数字，输入为字符串的时候，输出也应该为字符串。
这时，我们可以使用重载定义多个 reverse 的函数类型：

```
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```
上例中，我们重复定义了多次函数 reverse，前几次都是函数定义，最后一次是函数实现。在编辑器的代码提示中，可以正确的看到前两个提示。
注意，TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。

## 类型断言

有时候我们需要明码规定某个变量的类型，这个时候就需要断言。

### 语法：

```
<类型>值
```
或者
```
值 as 类型
```
在 tsx 语法（React 的 jsx 语法的 ts 版）中必须用后一种。

TypeScript入门教程里面的一个例子特别好
```
function getLength(something: string | number): number {
    if (something.length) {
        return something.length;
    } else {
        return something.toString().length;
    }
}
//这个时候会编译错误，因为something为number类型的时候，something.length就会报错，使用类型断言解决

function getLength(something: string | number): number {
    if ((<string>something).length) {
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}
//规定string的走 someting.length， 断言就是 果断判决  你说啥就是啥

```
- 断言的用法就是在需要断言的变量前面加上<Type>即可
- **断言只能断言联合类型里面存在的，不存在的是不能断言的，人家本来就没有那种可能，你不能硬扯**
```
function toBoolean(something: string | number): boolean {
    return <boolean>something;
}

// index.ts(2,10): error TS2352: Type 'string | number' cannot be converted to type 'boolean'.
//   Type 'number' is not comparable to type 'boolean'.
```

## 声明文件

ts是js的超集，那我们经常会引用第三方库，但是第三方库并不是我们写的ts,所以在引用第三方库的时候就得用声明文件，这样就可有获得代码补全、接口提示等功能

### 什么是声明语句

例如我们使用jQuery,常见的我们在html中直接通过script 引入了，就可以直接写$了。
例如我们通过 `$("#id")` 获取元素，但是ts并不认识 $是什么东西，这时候我们需要使用`declare var $:(slector:string)=>any`定义类型;

### 什么是声明文件

通常我们会把声明语句放在一个单独的文件里 例如（jQuery.d.ts）中，**声明文件必须以.d.ts为后缀**
```
/path/to/project
├── src
|  ├── index.ts
|  └── jQuery.d.ts
└── tsconfig.json
```
一般来说，ts 会解析项目中所有的 *.ts 文件，当然也包含以 .d.ts 结尾的文件。所以当我们将 jQuery.d.ts 放到项目中时，其他所有 *.ts 文件就都可以获得 jQuery 的类型定义了。

假如仍然无法解析，那么可以检查下 tsconfig.json 中的 files、include 和 exclude 配置，确保其包含了 jQuery.d.ts 文件。

这里只演示了全局变量这种模式的声明文件，假如是通过模块导入的方式使用第三方库的话，那么引入声明文件又是另一种方式了，将会在后面详细介绍。

### 第三方声明文件

前人种树后人乘凉，声明文件不用我们写，大佬都写好了，我们会npm就行，建议使用 `@types`管理，直接使用npm安装对应的 声明模块就可以了

```
npm install @types/jquery --save-dev
```

### 书写声明文件

有时候逼不得已，第三方库没有提供声明文件，这个时候只能靠自己了。

#### 全局变量

> 通过script标签，然后注册了个全局变量

使用全局变量的声明文件时，如果是以 `npm install @types/xxx --save-dev` 安装的，则不需要任何配置。如果是将声明文件直接存放于当前项目中，则建议和其他源码一起放到 src 目录下（或者对应的源码目录下）：

```
/path/to/project
├── src
|  ├── index.ts
|  └── jQuery.d.ts
└── tsconfig.json
```
如果没有生效，可以检查下 `tsconfig.json` 中的 `files`、`include` 和 `exclude` 配置，确保其包含了 `jQuery.d.ts` 文件。

全局变量声明的几种语法：
声明语句只能定义类型，不能定义具体的实现
- declare var
- declare let
- declare const
- declare function
  - 用来定义全局函数的类型
  - 在函数类型的声明语句中，函数重载也是支持的
- declare class
- declare enum
  - 外部枚举、
- declare namespace
  - 相当于声明一个module

#### 嵌套的命名空间

```
// src/jQuery.d.ts

declare namespace jQuery {
    function ajax(url: string, settings?: any): void;
    namespace fn {
        function extend(object: any): void;
    }
}
```

#### interface 和type

除了全局变量之外，可能有一些类型我们也希望能够暴露出来。在类型声明文件中，我们可以直接使用`interface`和`type`来声明一个全局的接口或类型

```
// src/jQuery.d.ts

interface AjaxSettings {
    method?: 'GET' | 'POST'
    data?: any;
}
declare namespace jQuery {
    function ajax(url: string, settings?: AjaxSettings): void;
}
```
使用：
```
// src/index.ts

let settings: AjaxSettings = {
    method: 'POST',
    data: {
        name: 'foo'
    }
};
jQuery.ajax('/api/post_something', settings);
```
`type`类比interface

#### 防止命名冲突

暴露在最外层的 `interface` 或 `type` 会作为全局类型作用于整个项目中，我们应该尽可能的减少全局变量或全局类型的数量。故最好将他们放到 `namespace`

```
// src/jQuery.d.ts

declare namespace jQuery {
    interface AjaxSettings {
        method?: 'GET' | 'POST'
        data?: any;
    }
    function ajax(url: string, settings?: AjaxSettings): void;
}
```
使用的时候加上jQuery前缀：
```
// src/index.ts

let settings: jQuery.AjaxSettings = {
    method: 'POST',
    data: {
        name: 'foo'
    }
};
jQuery.ajax('/api/post_something', settings);
```

#### 声明合并

假如 jQuery 既是一个函数，可以直接被调用 `jQuery('#foo')`，又是一个对象，拥有子属性 `jQuery.ajax()`（事实确实如此），那么我们可以组合多个声明语句，它们会不冲突的合并起来

```
// src/jQuery.d.ts

declare function jQuery(selector: string): any;
declare namespace jQuery {
    function ajax(url: string, settings?: any): void;
}
```

#### npm包

- 看看声明文件是否存在
  - 1.与该 npm 包绑定在一起。判断依据是 package.json 中有 types 字段，或者有一个 index.d.ts 声明文件。这种模式不需要额外安装其他包，是最为推荐的，所以以后我们自己创建 npm 包的时候，最好也将声明文件与 npm 包绑定在一起。
  - 2.发布到 @types 里。我们只需要尝试安装一下对应的 @types 包就知道是否存在该声明文件，安装命令是 npm install @types/foo --save-dev。这种模式一般是由于 npm 包的维护者没有提供声明文件，所以只能由其他人将声明文件发布到 @types 里了
- 如果没有找到声明文件，就只能自己写了
  - 创建一个 types 目录，专门用来管理自己写的声明文件，将 foo 的声明文件放到 types/foo/index.d.ts 中。这种方式需要配置下 tsconfig.json 中的 paths 和 baseUrl 字段。
  - 目录结构
    ```
    /path/to/project
    ├── src
    |  └── index.ts
    ├── types
    |  └── foo
    |     └── index.d.ts
    └── tsconfig.json
    ```

    tsconfig.json内容：
    ```
    {
        "compilerOptions": {
            "module": "commonjs",
            "baseUrl": "./",
            "paths": {
                "*": ["types/*"]
            }
        }
    }
    ```
- npm包声明文件的几种语法
  - `export`导出变量
    - npm 包的声明文件与全局变量的声明文件有很大区别。在 npm 包的声明文件中，使用 declare 不再会声明一个全局变量，而只会在当前文件中声明一个局部变量。只有在声明文件中使用 export 导出，然后在使用方 import 导入后，才会应用到这些类型声明。
      ```
       // types/foo/index.d.ts
        export const name: string;
        export function getName(): string;
        export class Animal {
            constructor(name: string);
            sayHi(): string;
        }
        export enum Directions {
            Up,
            Down,
            Left,
            Right
        }
        export interface Options {
            data: any;
        }
      ```
      对应的导入和使用模块是这样的：
      ```
      // src/index.ts

        import { name, getName, Animal, Directions, Options } from 'foo';

        console.log(name);
        let myName = getName();
        let cat = new Animal('Tom');
        let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
        let options: Options = {
            data: {
                name: 'foo'
            }
        };
      ```
    - 混用declare和export
      - 我们也可以使用`declare`先声明多个变量，最后再用`export`一次性导出。
      - ```
        // types/foo/index.d.ts

        declare const name: string;
        declare function getName(): string;
        declare class Animal {
            constructor(name: string);
            sayHi(): string;
        }
        declare enum Directions {
            Up,
            Down,
            Left,
            Right
        }
        interface Options {
            data: any;
        }

        export { name, getName, Animal, Directions, Options };
        ```
        注意，与全局变量的声明文件类似，`interface`前是不需要`declare`的。
  - `export namespace` 导出（含有子属性）对象
    - 与全局的declare namespace类似
        ```
        // types/foo/index.d.ts

        export namespace foo {
            const name: string;
            namespace bar {
                function baz(): string;
            }
        }
        ```
        ```
        // src/index.ts

        import { foo } from 'foo';

        console.log(foo.name);
        foo.bar.baz();
        ```
  - `export defalut` ES6默认导出
    - 只有 function、class 和 interface 可以直接默认导出，其他的变量需要先定义出来，再默认导出
    - ```
       // types/foo/index.d.ts

        declare enum Directions {
            Up,
            Down,
            Left,
            Right
        }

        export default Directions; 
      ```
      针对这种默认导出，我们一般会将导出语句放在整个声明文件的最前面
  - `export =` 是commmonjs导出模块
    - 在 commonjs 规范中，我们用以下方式来导出一个模块：
    ```
    // 整体导出
    module.exports = foo;
    // 单个导出
    exports.bar = bar;  


    // types/foo/index.d.ts

    export = foo;

    declare function foo(): string;
    declare namespace foo {
        const bar: number;
    }
    ```
#### UMD库

使用 `export as namespace`
```
// types/foo/index.d.ts

export as namespace foo;
export = foo;

declare function foo(): string;
declare namespace foo {
    const bar: number;
}
```

当与`export defalut`
```
// types/foo/index.d.ts

export as namespace foo;
export default foo;

declare function foo(): string;
declare namespace foo {
    const bar: number;
}
```

#### 直接扩展全局变量

有的第三方库扩展了一个全局变量，可是此全局变量的类型却没有相应的更新过来，就会导致 ts 编译错误，此时就需要扩展全局变量的类型。比如扩展 `String` 类型

```
interface String {
    prependHello(): string;
}

'foo'.prependHello();
```
通过声明合并，使用 `interface String` 即可给 `String` 添加属性或方法。
也可以使用 `declare namespace` 给已有的命名空间添加类型声明
```
// types/jquery-plugin/index.d.ts

declare namespace JQuery {
    interface CustomOptions {
        bar: string;
    }
}

interface JQueryStatic {
    foo(options: JQuery.CustomOptions): string;
}
```

#### 在npm包或UMD库中扩展全局变量

如之前所说，对于一个 npm 包或者 UMD 库的声明文件，只有 `export` 导出的类型声明才能被导入。所以对于 npm 包或 UMD 库，如果导入此库之后会扩展全局变量，则需要使用另一种语法在声明文件中扩展全局变量的类型，那就是 `declare global`。

`declare global`

使用 `declare global` 可以在 npm 包或者 UMD 库的声明文件中扩展全局变量的类型

```
// types/foo/index.d.ts

declare global {
    interface String {
        prependHello(): string;
    }
}

export {};
```
注意即使此声明文件不需要导出任何东西，仍然需要导出一个空对象，用来告诉编译器这是一个模块的声明文件，而不是一个全局变量的声明文件。

#### 模块插件

有时通过`import`导入一个模块插件，可以改变另一个原有模块的结构。此时如果原有模块已经有了类型声明文件，而插件模块没有声明文件，就会导致类型不完整，缺少插件部分的类型，ts提供了一个语法`declare module`，它可以用来扩展有模块的类型。

`declare module`

如果是需要扩展原有模块的话，需要在类型声明文件中引用原有模块，再使用`declare module`扩展原有模块
```
// types/moment-plugin/index.d.ts

import * as moment from 'moment';

declare module 'moment' {
    export function foo(): moment.CalendarKey;
}
```

```
// src/index.ts

import * as moment from 'moment';
import 'moment-plugin';

moment.foo();
```

`declare module`也可用于在一个文件中一次性声明多个模块的类型
```
// types/foo-bar.d.ts

declare module 'foo'{
    export interface Foo{
        foo:string;
    }
}

declare module 'bar'{
    export function bar():string;
}
```

```
// src/index.ts
import { Foo } from 'foo';
import * as bar from 'bar';

let f: Foo;
bar.bar();
```

#### 声明文件中的依赖

- 一个声明文件有时会依赖另一个声明文件中的类型，比如在前面的`declare module`的例子中，我们就在声明文件中导入了`moment`,并且使用了`moment.CalendarKey`这个类型：
```
// types/moment-plugin/index.d.ts

import * as moment from 'moment';

declare module 'moment'{
    export function foo(): moment.CalendarKey();
}
```
除了可以再声明文件中通过`import`导入另一个文件中的类型之外，还有一个语法也可以用来导入另一个声明文件，那就三斜线命令。
  - 三斜线指令
    - 类似声明文件中的`import`，它可以用来导入另个声明文件。与`import`的区别是，当且仅当在以下几个场景中，我们才需要使用三斜线指令代替`import`
      - 当我们书写一个全局变量的声明文件时
      - 当我们需要依赖一个全局变量的声明文件时
  - 书写一个全局变量的声明文件
    - 再全局变量的声明文件中，是不允许出现`import`,`export`关键字的，一旦出现，那么他就会被视为一个npm包或UMD库，就不再是全局变量的声明文件了。故当我们再书写一个全局变量的文件时，如果需要引用另一个库的类型，那么就必须用三斜线指令
    ```
    // types/jquery-plugin/index.d.ts

    /// <reference types="jquery" />

    declare function foo(options: JQuery.AjaxSettings): string;
    ```
    三斜线指令的语法如上，`///` 后面使用 xml 的格式添加了对 `jquery` 类型的依赖，这样就可以在声明文件中使用 `JQuery.AjaxSettings` 类型了
    注意，三斜线指令必须放在文件的最顶端，三斜线指令的前面只允许出现单行或多行注释。
  - 依赖一个全局变量的声明文件
    - 在另一个场景下，当我们需要依赖一个全局变量的声明文件时，由于全局变量不支持通过`import`导入，当然也就必须使用三斜线指令来引入了
    ```
    // types/node-plugin/index.d.ts

    /// <reference types="node" />

    export function foo(p: NodeJS.Process): string;
    ```
    ```
    // src/index.ts

    import { foo } from 'node-plugin';

    foo(global.process);
    ```
    在上面的例子中，我们通过三斜线指令引入了`node`的类型，然后在声明文件中使用了`NodeJS.Porcess`这个类型。最后再使用到`foo`的时候，传入了`node`中的全局变量`process`。
    由于引入的`node`中的类型都是全局变量的类型，它们是没有办法通过`import`来导入的，所以这种场景下也只能通过三斜线指令来引入了。
    以上两种使用场景下，都是由于需要书写或者需要依赖全局变量的声明文件，所以必须使用三斜线指令。再掐他的一些不是必要使用三斜线的指令的情况下，就都需要使用`import`来导入。
  - 拆分声明文件
    - 当我们的全局变量的声明文件太大的时，可以通过拆封为多个文件，然后再一个入口文件中将它们一一引入，提高代码的可维护性。比如`jQuery`的文件就是这样的：
    ```
    // node_modules/@types/jquery/index.d.ts

    /// <reference types="sizzle">
    /// <reference path="JQueryStatic.d.ts">
    /// <reference path="JQuery.d.ts">
    /// <reference path="misc.d.ts">
    /// <reference path="legacy.d.ts">

    export = jQuery;
    ```
    其中用到了`types`和`path`两种不同的指令。它们的区别是：`types`用于声明对另一个库的依赖，而`path`用于声明对另一个文件的依赖。
    上例中，`sizzle`是与`jquery`平行的另一个库，所以需要使用`types="sizzle"`来声明对它的依赖。而其他的三斜线指令就是将`jquery`的声明拆分到不同的文件中了，然后再这个入口文件中使用 `path="foo"`将它们一一引入。

#### 自动生成声明文件

如果库的源码本身就是由ts写的，那么在使用`tsc`脚本将ts编译成js的时候，添加`declaration`选项，就可以同时生成`.d.ts`声明文件了。

我们可以再命令行中添加`--declaration`简写`-d`，或者再tsconfig.json中添加`declaration`选项。
- declarationDir 设置生成 .d.ts 文件的目录
- declarationMap 对每个 .d.ts 文件，都生成对应的 .d.ts.map（sourcemap）文件
- emitDeclarationOnly 仅生成 .d.ts 文件，不生成 .js 文件

## 内置对象

Javascript中有很多内置对象，它们可以直接在TypeScript中当做定义好了的类型。

### ECMAScript的内置对象

`Boolean`,`Error`,`Date`,`RegExp`等。

```
let b: Boolean = new Boolean(1);
let e: Error = new Error('Error occurred');
let d: Date = new Date();
let r: RegExp = /[a-z]/;
```

### DOM和BOM的内置对象

`Document`,`HTMLElement`,`Event`,`NodeList`等。

```
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click', function(e: MouseEvent) {
  // Do something
});
```
可以在[TypeScript核心库的定义文件中查看](https://github.com/Microsoft/TypeScript/tree/master/src/lib)

### TypeScript写nodejs

Node.js不是内置对象的一部分，如果想用TypeScript写Node.js,则需要引入第三方声明文件

```
npm install @types/node --save-dev
```

# 进阶

##　类型别名

类型别名用来给一个类型起个新名字。

```
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}
```
上例中，我们使用`type`创建类型别名，类型别名常用于联合类型

##  字符串字面量类型用来约束取值只能是某几个字符串中的一个

```
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}

handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
handleEvent(document.getElementById('world'), 'dbclick'); // 报错，event 不能为 'dbclick'

// index.ts(7,47): error TS2345: Argument of type '"dbclick"' is not assignable to parameter of type 'EventNames'.
```
上例中，我们使用`type`定义了一个字符串字面量类型`EventNames`，它只能取三种字符串中的一种。
注意，类型别名与字符串字面量类型都是使用`type`进行定义。

## 元组

数组合并了相同类型的对象，而元组(Tuple)合并了不同类型的对象。

这只是说了个大概如果 你说 any[],这是个例外，但是它也没有办法规定，数组中具体的某一项的类型，而元组就是实现了这个。

```
let xcatliu: [string, number] = ['Xcat Liu', 25];
```

当访问或者赋值一个已知的索引的元素的时，会得到正确的类型：

```
let xcatliu: [string, number];
xcatliu[0] = 'Xcat Liu';
xcatliu[1] = 25;

xcatliu[0].slice(1);
xcatliu[1].toFixed(2);
```
也可以只赋值其中一项

```
let xcatliu: [string, number];
xcatliu[0] = 'Xcat Liu';
```

但是当直接对元组类型的变量进行初始化或者赋值的时候，需要提供所有元组类型中制定的项，
就是下面这种赋值方法需要全部都填上

let xcatliu: [string, number];
xcatliu = ['Xcat Liu', 25];

### 越界的元素

当添加越界的元素时，它的类型会被限制为元组中每个类型的联合类型：

白话文 就是说 如果数组长度为2，你要添加第三个元素的时候，类型只能是元组规定的类型，也就是所说的联合类型：
```
let xcatliu: [string, number];
xcatliu = ['Xcat Liu', 25];
xcatliu.push('http://xcatliu.com/');
xcatliu.push(true);

// index.ts(4,14): error TS2345: Argument of type 'boolean' is not assignable to parameter of type 'string | number'.
//   Type 'boolean' is not assignable to type 'number'.
```

## 枚举

枚举(Enum)类型用于取值被限定在一定的范围内的场景，比如一周只能有七天，颜色限定为红绿蓝等。

枚举使用`enum`关键字来定义：
```
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};
```
上面例子会被编译成为：

```
var Days;
(function (Days) {
    Days[Days["Sun"] = 0] = "Sun";
    Days[Days["Mon"] = 1] = "Mon";
    Days[Days["Tue"] = 2] = "Tue";
    Days[Days["Wed"] = 3] = "Wed";
    Days[Days["Thu"] = 4] = "Thu";
    Days[Days["Fri"] = 5] = "Fri";
    Days[Days["Sat"] = 6] = "Sat";
})(Days || (Days = {}));


```
![](/assets/枚举days.png)

### 手动赋值

我们也可以给枚举项手动赋值
```
enum Days {Sun = 7, Mon = 1, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 7); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true
```

上面的例子中，未手动赋值的枚举项会接着上一个枚举项递增。

如果未手动赋值的枚举项与手动赋值的重复了，TypeScript是不会察觉到这一点的

```
enum Days {Sun = 3, Mon = 1, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 3); // true
console.log(Days["Wed"] === 3); // true
console.log(Days[3] === "Sun"); // false
console.log(Days[3] === "Wed"); // true
```
所以使用的时候需要注意，最好不要出现这种覆盖的情况。

手动赋值的枚举项可以不是数字，此时需要使用类型断言来让 tsc 无视类型检查 (编译出的 js 仍然是可用的)：
```
enum Days {Sun = 7, Mon, Tue, Wed, Thu, Fri, Sat = <any>"S"};
```

当然，手动赋值的枚举项也可以为小数或负数，此时后续未手动赋值的项的递增步长仍为 1：
```
enum Days {Sun = 7, Mon = 1.5, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 7); // true
console.log(Days["Mon"] === 1.5); // true
console.log(Days["Tue"] === 2.5); // true
console.log(Days["Sat"] === 6.5); // true
```

### 常数项和计算所得项

枚举项有两种类型：常数项（constant member）和计算所得项（computed member）。
```
//计算所得项
enum Color {Red, Green, Blue = "blue".length};
```
**计算所得项的后面不能是未手动赋值的项。否则报错**
当满足以下条件时，枚举成员被当作是常数：
- 不具有初始化函数并且之前的枚举成员是常数。在这种情况下，当前枚举成员的值为上一个枚举成员的值加1。但第一个枚举元素是个例外。如果它没有初始化方法，那么它的初始值为0.
- 枚举成员使用常数枚举表达式初始化。常数枚举表达式是Typescript表达式的子集，它可以再编译阶段求值，当一个表达式满足下面条件之一时，它就是个常数表达式：
  - 数字字面量
  - 引用之前定义的常数枚举成员（可以是在不同的枚举类型中定义的）如果这个成员是在同一个枚举类型中定义的，可以使用非限定义名来引用
  - 带括号的常数枚举表达式
  - `+`，`-`，`~`一元运算符应用于常数枚举表达式
  - `+`,`-`,`*`,`/`,`%`,`<<`,`>>`,`>>>`,`&`,`|`,`^`二元运算符，常数枚举表达式做为其一个操作对象。若常数枚举表达式求值后为NaN或Infinity，则会在编译阶段报错

所有其他情况的枚举成员被当作是需要计算得出的值。

### 常数枚举

常数枚举是使用`const enum`定义的枚举类型：
```
const enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```
常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且不能包含计算成员。

上例编译结果为：
```
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

### 外部枚举

外部枚举（Ambient Enums）是使用`declare enum`定义的枚举类型：
```
declare enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```
上例的编译结果为

```
var directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

外部枚举和声明语句一样，常出现再声明文件中。
同时使用`declare`和`const`也是可以的：
```
declare const enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```
编译结果：
```
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

## 类

### 类的概念

- 类（Class）：定义了一件事物的抽象特点，包含它的属性和方法
- 对象（Object）：类的实例，通过new生成
- 面向对象（OOP）的三大特性：封装、继承、多肽
- 封装（Encapsulation）：将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问对象，同时也保证了外界无法任意更改对象内部的数据
- 继承（Inheritance）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性
- 多肽（Polymorphism）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如`Cat`和`Dog`都继承自`Animal`，但是分别实现了自己的`eat`方法。此时针对某一个实例，我们无需了解它是`Cat`还是`Dog`，就可以直接调用`eat`方法，程序会自动判断出来应该如何执行`eat`
- 存取器（getter&setter）：用以改变属性的读取和赋值行为
- 修饰器（Modifiers）：修饰符是一些关键字，用于限定成员或类型的性质。比如`public`表示公有属性或方法
- 抽象类（Abstract Class）：抽象类是提供其他继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须再子类中被实现
- 接口（Interfaces）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口。

### ES6中类的用法

#### 属性和方法
使用`class`定义类，使用`constructor`定义构造函数

通过`new`生成新实例的时候，会自动调用构造函数

```
class Animal {
    constructor(name){
        this.name  = name;
    }
    sayHi(){
        return `Myname is ${this.name}`;
    }
}

let  dog = new Animal('dog');
console.log(dog.sayHi())//My name is dog
```

#### 类的继承
使用`extends`关键字实现继承，子类中使用`super`关键字来调用父类的构造函数和方法。
```
class Cat extends Animal{
    constructor(name){
        super(name);//调用父类的 constructor(name)
        console.log(this.name)
    }
    sayHi(){
        return 'Meow,' + super.sayHi();//调用父类的sayHi()
    }
}

let c = new Cat('Tom');//Tom
console.log(c.sayHi()); Meow,My  name is Tom
```
#### 存取器

使用 getter和setter 可以改变属性的赋值和读取行为：

```
class Animal{
    constructor(name){
        this.name = name;
    }
    get name(){
        return 'Jack';
    }
    set name(){
        console.log('setter:'+ value );
    }
}

let a = new Animal('Kitty')；//setter:Kitty
a.name = 'Tom';//setter:Tom
console.log(a.name);// Jack
```

#### 静态方法

使用`static`修饰符修饰的方法为静态方法，它们不需要实例化，而是直接通过类来调用 ：

```
class Animal {
    static isAnimal(a){
        return a instanceof Animal;
    }
}
let a = new Animal('Jack');
Animal.isAnimal(a);//true
a.isAnimal(a);//TypeError:a.isAnimal is  not a function
```

#### ES7中类的用法

ES7中有一些关于类的提案，TypeScript也实现了它们，这里做一个简单的介绍。

- 实例的属性
Es6中实例的属性只能通过构造函数中的`this.xxx`来定义，ES7提案中可以直接再类里面定义：

```
Class Animal {
    name = 'Jack';
    constructor(){

    }
}
let a = new Animal();
console.log(a.name);//Jack
```
- 静态属性

ES7提案中，可以使用`static`定义一个静态属性：
```
class Animal {
    static num = 42;

    constructor() {
        // ...
    }
}

console.log(Animal.num); // 42
```

###  TypeScript中类的用法

#### public private和protected

TypeScript可以使用三种访问修饰符，分别是`public`、`private`、`protected`。

- `public`修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是`public`的
- `private`修饰的属性或方法是私有的，不能在声明它的类的外部访问
- `protected`修饰的属性或方法是受保护的，他和`private`类似，区别是它在子类中也是允许被访问的

例子：
```
class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';
console.log(a.name); // Tom
```

很多时候我们希望属性是无法被直接存取的，这个时候就用到了`private`了：

```
class Animal {
    private name;
    public constructor(name) {
        this.name = name;
    }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';

// index.ts(9,13): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
// index.ts(10,1): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

使用`private`修饰的属性或方法,在子类中也是不允许访问的：

```
class Animal {
    private name;
    public constructor(name) {
        this.name = name;
    }
}

class Cat extends Animal {
    constructor(name) {
        super(name);
        console.log(this.name);
    }
}

// index.ts(11,17): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

而如果是用`protected`修饰，则允许在子类中访问：

```
class Animal {
    protected name;
    public constructor(name) {
        this.name = name;
    }
}

class Cat extends Animal {
    constructor(name) {
        super(name);
        console.log(this.name);
    }
}
```

#### 抽象类

`abstract`用于定义抽象类和其中的抽象方法。

什么是抽象类？

首先，抽象类是不允许实例化的：

```
abstract class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
    public abstract sayHi();
}

let a = new Animal('Jack');

// index.ts(9,11): error TS2511: Cannot create an instance of the abstract class 'Animal'.
```

其次，抽象类中的方法必须被子类实现：

```
abstract class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
    public abstract sayHi();
}

class Cat extends Animal {
    public eat() {
        console.log(`${this.name} is eating.`);
    }
}

let cat = new Cat('Tom');

// index.ts(9,7): error TS2515: Non-abstract class 'Cat' does not implement inherited abstract member 'sayHi' from class 'Animal'.
```

#### 类的类型

给类加上TypeScript的类型很简单，与接口类似：
```
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    sayHi(): string {
      return `My name is ${this.name}`;
    }
}

let a: Animal = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```

### 类与接口

#### 类实现接口

实现（implements）是面向对象中的一个重要的概念 。一般来讲，一个类只能继承另一个类，有时候不同类之间可以有一些共有的特性，这时候就可以把特性提取成接口（interfaces），用`implements`关键字来实现。这个特性大大提高了面向对象的灵活性。
举例来说，门是一个类，防盗门是门的子类。如果防盗门有一个报警器的功能，我们可以简单的给防盗门添加一个报警方法。这时候如果有另外一个类，车，也有报警的功能，就可以考虑把报警器提取出来，作为一个接口，防盗门和车都去实现它：
```
interface Alarm {
    alert();
}

class Door {
}

class SecurityDoor extends Door implements Alarm {
    alert() {
        console.log('SecurityDoor alert');
    }
}

class Car implements Alarm {
    alert() {
        console.log('Car alert');
    }
}
```

一个类可以实现多个接口

```
interface Alarm {
    alert();
}

interface Light {
    lightOn();
    lightOff();
}

class Car implements Alarm, Light {
    alert() {
        console.log('Car alert');
    }
    lightOn() {
        console.log('Car light on');
    }
    lightOff() {
        console.log('Car light off');
    }
}
```

#### 接口继承接口

接口与接口之间可以是继承关系：
```
interface Alarm {
    alert();
}

interface LightableAlarm extends Alarm{
    lightOn();
    lightOff();
}
```

#### 接口继承类

接口也可以继承类：

```
class Point{
    x:number;
    y:number;
}

interface Point3d extends Point{
    z:number;
}

let point3d:Point3d = {x:1 ,y:2, z:3}
```

#### 混合类型

之前了解过，我们可以使用接口的方式来定义一个函数需要符合的形状：

```
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```

有时候，一个函数还可以有自己的属性和方法：

```
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

### 泛型

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

首先，我们来实现一个函数`createArray`，它可以创建一个制定长度的数组，同时将每一项都填充一个默认值：

```
function createArray(length: number, value: any): Array<any> {
    let result = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']
```

这段代码编译不会报错，但是一个显而易见的缺陷是，它并没有准确的定义返回值的类型：

`Array<any>`允许数组的每一项都为任意类型。但是我们预期的是，数组中每一项都应该是输入的`value`的类型

这时候，泛型就派上用场了：

```
function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray<string>(3, 'x'); // ['x', 'x', 'x']
```
#### 多个类型参数

定义泛型的时候，可以一次定义多个类型参数：

```
function swap<T, U>(tuple: [T, U]): [U, T] {
    return [tuple[1], tuple[0]];
}

swap([7, 'seven']); // ['seven', 7]
```

上例中，我们定义了一个`swap`函数，用来交换输入的元组。

#### 泛型约束

再函数内部使用泛型变量的时候，由于事先不知道它是那种类型，所以不能随意的操作它的属性或方法：

```
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);
    return arg;
}

// index.ts(2,19): error TS2339: Property 'length' does not exist on type 'T'.
```
多个类型参数之间也可以互相约束：
```
function copyFields<T extends U, U>(target: T, source: U): T {
    for (let id in source) {
        target[id] = (<T>source)[id];
    }
    return target;
}

let x = { a: 1, b: 2, c: 3, d: 4 };

copyFields(x, { b: 10, d: 20 });
```
上例中，我们使用了两个类型参数，其中要求 T 继承 U，这样就保证了 U 上不会出现 T 中不存在的字段

#### 泛型接口

之前学习过，可以使用接口的方式来定义一个函数需要符合的形状：

```
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```
当然也可以使用含有泛型的接口来定义函数的形状：

```
interface CreateArrayFunc{
    <T>(length:number,value:T):Array<T>;
}

let createArray:CreateArrayFunc;
createArray = function<T>(length:number,value:T):Array<T>{
    let result:T[] = [];
    for(let i = ;i < length;i++){
        result[i] = value;
    }
    return result;
}

createArray(3,'x');//['x','x','x']
```
进一步，我们可以把泛型参数提前到接口名上：

```
interface CreateArrayFunc<T> {
    (length: number, value: T): Array<T>;
}

let createArray: CreateArrayFunc<any>;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']
```
注意，此时在使用泛型接口的时候，需要定义泛型的类型。

#### 泛型类

与泛型接口类似，泛型也可以用于类的类型定义中：

```
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```
#### 泛型参数的默认类型

在 TypeScript2.3以后，我们可以为泛型中的类型参数指定默认类型。当使用泛时没有在代码中直接指定类型参数，从实际值参数中也无法推测出时，这个默认类型就会起作用
```
function createArray<T = string>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}
```

###  声明合并

如果定义了两个相同名字的函数、接口或类，那么它们会合并成一个类型：

#### 函数的合并

我们可以使用重载定义多个函数类型：
```
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

#### 接口的合并

接口中的属性在合并的时会简单的合并到一个接口中：
```
interface Alarm{
    price:number;
}
interface Alarm{
    weight:number;
}
```

相当于：
```
interface Alarm {
    price:number;
    weight:number;
}
```

注意，合并的属性的类型必须是唯一的：
```
interface Alarm {
    price: number;
}
interface Alarm {
    price: number;  // 虽然重复了，但是类型都是 `number`，所以不会报错
    weight: number;
}
```

```
interface Alarm {
    price: number;
}
interface Alarm {
    price: string;  // 类型不一致，会报错
    weight: number;
}

// index.ts(5,3): error TS2403: Subsequent variable declarations must have the same type.  Variable 'price' must be of type 'number', but here has type 'string'.
```

接口中方法的合并，与函数的合并一样：
```
interface Alarm {
    price: number;
    alert(s: string): string;
}
interface Alarm {
    weight: number;
    alert(s: string, n: number): string;
}
```
相当于
```
interface Alarm {
    price: number;
    weight: number;
    alert(s: string): string;
    alert(s: string, n: number): string;
}
```