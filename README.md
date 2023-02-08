# ralph-book

The ralph Programming Language

<!-- TOC -->
* [ralph-book](#ralph-book)
  * [简介](#简介)
  * [项目结构](#项目结构)
  * [基础语法](#基础语法)
    * [命名规范](#命名规范)
    * [缩进](#缩进)
    * [关键字](#关键字)
    * [作用域](#作用域)
    * [语法关系](#语法关系)
    * [注释](#注释)
  * [变量](#变量)
  * [数据类型](#数据类型)
  * [字面量](#字面量)
  * [运算符](#运算符)
  * [语句](#语句)
    * [判断语句/表达式](#判断语句表达式)
    * [循环语句](#循环语句)
      * [For loop](#for-loop)
      * [While loop](#while-loop)
    * [返回语句](#返回语句)
    * [复合语句](#复合语句)
  * [方法](#方法)
    * [方法定义](#方法定义)
    * [方法签名](#方法签名)
    * [方法调用](#方法调用)
    * [内置方法](#内置方法)
  * [合约](#合约)
    * [合约定义](#合约定义)
    * [修饰符](#修饰符)
    * [枚举](#枚举)
    * [字段](#字段)
    * [event](#event)
    * [Contract Creation inside a Contract](#contract-creation-inside-a-contract)
  * [接口](#接口)
  * [继承](#继承)
  * [脚本（执行入口）](#脚本执行入口)
  * [注解](#注解)
    * [方法注解](#方法注解)
        * [Using Approved Assets](#using-approved-assets)
        * [Using Contract Assets](#using-contract-assets)
        * [External Call Check](#external-call-check)
    * [字段或参数注解](#字段或参数注解)
  * [类型转化](#类型转化)
  * [Map](#map)
  * [错误处理](#错误处理)
  * [调试方法](#调试方法)
  * [特殊用法](#特殊用法)
  * [编译器](#编译器)
  * [编译](#编译)
  * [部署](#部署)
  * [工具](#工具)
  * [样例](#样例)
  * [参考](#参考)
  * [Q&A](#qa)
<!-- TOC -->


## 简介

Ralph 是 Alephium 区块链的智能合约编程语言，专注目标：安全、简单、效率。本教程提供编写清晰、惯用且安全的代码规范。

## 项目结构

- 单项目多层级结构(推荐)

```shell
project1
├── artifacts
│    ├── add
│    │    └── add.ral.json
│    ├── greeter
│    │    └── greeter.ral.json
│    ├── main.ral.json
│    └── sub
│        └── sub.ral.json
└── contracts
    ├── add
    │    └── add.ral
    ├── greeter
    │    ├── greeter.ral
    │    └── greeter_interface.ral
    ├── main.ral
    └── sub
        └── sub.ral

```

- 单项目单层级结构

```shell
project2
├── action.ral
├── artifacts
│    ├── action.ral.json
│    ├── foo.ral.json
│    ├── main.ral.json
│    └── math.ral.json
├── foo.ral
├── main.ral
└── math.ral

```

- 多项目多层级结构

```shell

project3
├── project1
│    ├── artifacts
│    │    ├── add
│    │    │    └── add.ral.json
│    │    ├── greeter
│    │    │    └── greeter.ral.json
│    │    ├── main.ral.json
│    │    └── sub
│    │        └── sub.ral.json
│    └── contracts
│        ├── add
│        │    └── add.ral
│        ├── greeter
│        │    ├── greeter.ral
│        │    └── greeter_interface.ral
│        ├── main.ral
│        └── sub
│            └── sub.ral
└── project2
    ├── action.ral
    ├── artifacts
    │    ├── action.ral.json
    │    ├── foo.ral.json
    │    ├── main.ral.json
    │    └── math.ral.json
    ├── foo.ral
    ├── main.ral
    └── math.ral
```

[//]: # "## 文件结构"

## 基础语法

### 命名规范

标识符由[a-zA-Z0-9!_]构成，驼峰命名法。

- Contract、Interface、TxScrip、Enum 结构须以大写字母开头,有[a-zA-Z0-9]构成。
- 方法：小写字母开头。
- 变量：小写字母开头。
- 内置方法: 以'!'为后结尾。

### 缩进

- 1 个 Tab = 4 个 Space

### 关键字

[源码](https://github.com/alephium/alephium/blob/master/ralph/src/main/scala/org/alephium/ralph/Lexer.scala)

| Contract | Interface | TxScript | AssetScript | enum       | Abstract | let  | mut     | const   | if   | else |
| -------- | --------- | -------- | ----------- | ---------- | -------- | ---- | ------- | ------- | ---- | ---- |
| while    | for       | return   | extends     | implements | event    | emit | unused  | using   | alph | ALPH |
| fn       | pub       | I256     | true        | false      | Bool     | U256 | ByteVec | Address |      |      |

### 作用域

- 方法作用域：方法内部范围。
- 结构作用域：合约内部范围 + 继承范围。
- 全局作用域：Contract、Interface、TxScript 等命名需全局唯一。

### 语法关系

```text
.(project)
│
├── Contract
│    ├── enum
│    │    └── field
│    ├── event
│    ├── field
│    ├── fn
│    │    ├── stmt
│    │    └── variable
│    └── variable
├── Interface
│    └── function sig
├── Txscript
│    ├── event
│    ├── field
│    ├── fn
│    │    ├── stmt
│    │    └── variable
│    └── variable
...
```

### 注释

- 单行注释

```rust
// hello world

let lucky_number = 7 // I’m feeling lucky today
```

- 多行注释

```rust
// So we’re doing something complicated here, long enough that we need
// multiple lines of comments to do it! Whew! Hopefully, this comment will
// explain what’s going on.
```

## 变量

A function cannot have duplicate variable definitions, and the variable name in the function cannot be the same as the contract field name.

```rust
fn foo() -> () {
  // `a` is immutable, and it cannot be reassigned
  let a = 10
  a = 9 // ERROR

  // `b` is mutable, and it can be reassigned
  let mut b = 10
  b = 9
}

fn bar() -> (U256, Boolean) {
  return 1, false
}

fn baz() -> () {
  // Both `a` and `b` are immutable
  let (a, b) = bar()
  // `c` is immutable, but `d` is mutable
  let (c, mut d) = bar()
  // Ignore the first return value of the function `bar`
  let (_, e) = bar()
}
```

- 常量

```rust
const foo = 12
```

- 不可变变量

```rust
let foo = 1
foo = 2 // error
```

- 可变变量

```rust
let mut foo = 1
foo = 2
```

- 可变参数

```rust
fn foo(mut number: I256) -> (){
    number = 32
}
```

- 可变字段

```rust
Contrct foo(mut number: I256){
    fn foo() -> {
        number = 32
    }
}
```

## 数据类型

Ralph 是一种静态类型的语言，你不需要为局部变量和常量指定类型，编译器会自动进行类型推导。
Ralph 的类型都是值类型，因此，当被用作函数参数或赋值时，总是被复制。目前，Ralph 支持以下数据类型：

- 基本类型

| 类型    | 长度    | 描述         |
| ------- | ------- | ------------ |
| Bool    | 1 byte  | 布尔类型     |
| I256    | 32 byte | 有符号整型   |
| U256    | 32 byte | 无符号整型   |
| ByteVec | \*      | 字节数组类型 |
| Address | 20 byte | 地址类型     |

- 数组类型

形式：[type; len]

- 结构类型

通过关键字 Contract、Interface、TxScript、AssetScript、enum、Abstract 定义的结构类型。

## 字面量

- Bool

```rust
// The type fo `a` and `b` is Boolean.
let a = false
let b = true
```

- I256

```rust
// The type of `a` ... `d` is I256.
let a = -10
let b = 10i
let c = -1_000_000_000
let d = -1e18
```

- U256

```rust
// The type of `a` ... `d` is U256.
let a = 10
let b = 10u
let c = 1_000_000_000
let d = 1e18
```

- ByteVec

```rust
// ByteVec literals must start with `#` followed by a hex string.
let a = #00112233
// ByteVec concatenation
let b = #0011 ++ #2233 // `b` is #00112233
// Empty ByteVec
let c = #
```

- Address

```rust
// Address literals must start with `@` followed by a valid base58 encoded Alephium address.
let a = @1DrDyTr9RpRsQnDnXo2YRiPzPW4ooHX5LLoqXrqfMrpQH
```

- 数组

```rust
// The type of `a0` is [U256; 4]
let a0 = [0, 1, 2, 3]

// The type of `a1` is [[U256, 2]; 2]
let a1 = [[0, 1], [2, 3]]

// The type of `a2` is [I256; 3]
let a2 = [0i; 3]

// The type of `a3` is [ByteVec; 4]
let a3 = [#00, #11, #22, #33]
```

## 运算符

- 算术运算符

| 类型     | 描述 | 实例 |
| -------- | ---- | ---- |
| +        |      |      |
| -        |      |      |
| \*       |      |      |
| /        |      |      |
| %        |      |      |
| ⊕ or `+` |      |      |
| ⊖ or `-` |      |      |
| ⊗ or `*` |      |      |

- 关系运算符

| 类型 | 描述 | 实例 |
| ---- | ---- | ---- |
| ==   |      |      |
| !=   |      |      |
| <    |      |      |
| <=   |      |      |
| >    |      |      |
| >=   |      |      |

- 逻辑运算符

| 类型 | 描述 | 实例 |
| ---- | ---- | ---- |
| &&   |      |      |
| !    |      |      |
| ｜｜ |      |      |

- 位运算符

| 类型 | 描述 | 实例 |
| ---- | ---- | ---- |
| <<   |      |      |
| >>   |      |      |
| &    |      |      |
| ^    |      |      |
| ｜   |      |      |

- 连接运算符

| 类型 | 描述 | 实例 |
| ---- | ---- | ---- |
| ==   |      |      |

## 语句

### 判断语句/表达式

```rust
if (){
    ...
}else{
    ...
}
```

- 语句

```rust
fn foo() -> ByteVec {
  // If else statement
  if (a == 0) {
    return #00
  } else if (a == 1) {
    return #01
  } else {
    return #02
  }
}
```

- 表达式

```rust
fn foo() -> ByteVec {
  return if (a == 0) #00 else if (a == 1) #01 else #02
}
```

### 循环语句

#### For loop

```rust
// For loop
fn foo() -> () {
  for (let mut index = 0; index <= 4; index = index + 1) {
    bar(index)
  }
}
```

#### While loop

```rust
// While loop
fn foo() -> () {
  let mut index = 0
  while (index <= 4) {
    bar(index)
    index += 1
  }
}
```

```rust
fn foo() -> () {
  let value = 0
  for (let mut index = 0; index <= 4; index = index + 1) {
    let value = 0 // ERROR, duplicated variable definitions
    // ...
  }
}
```

**注意**:

- `break` and `continue` statements are not supported in `for-loop` and `while-loop`.
- In Ralph, each function has only one scope, so you can not define duplicated variables in the `while` or `for` block:

### 返回语句

- 无返回值

```rust
fn foo() -> () {
  ...
}
```

- 单一返回值

```rust
fn foo() -> U256 {
...
return 1
}
```

- 多值返回

```rust
fn foo() -> (U256, Boolean, ByteVec) {
  return 1, false, #00
}
```

### 复合语句

- 定义语句
- 表达式语句
- 返回语句
- 判断语句
- 循环语句

## 方法

Functions are the executable units of code, you can also define functions inside a contract.

### 方法定义

```
[pub] fn name(...) -> (...){
    ...
}
```

### 方法签名

```rust
// Public function, which can be called by anyone
pub fn foo() -> ()

// Private function, which can only be called inside the contract
fn foo() -> ()

// Function takes 1 parameter and has no return values
fn foo(a: U256) -> ()

// Function takes 2 parameters and returns 1 value
fn foo(a: U256, b: Boolean) -> U256

// Function takes 2 parameters and returns multiple values
fn foo(a: U256, b: Boolean) -> (U256, ByteVec, Address)
```

### 方法调用

Functions of the current contract can be called directly ('internally') or recursively:

```rust
Contract Foo() {
  fn foo(v: U256) -> () {
    if (v == 0) {
      return
    }
    // Internal function call
    bar()
    // Recursive function call
    foo(v - 1)
  }

  fn bar() -> () {
    // ...
  }
}
```

Functions can also be called externally using the `bar.func()` notation, where `bar` is a contract instance and `func` is a function belonging to `bar`:

```rust
Contract Bar() {
  pub fn func() -> U256 {
    // ...
  }
}

Contract Foo() {
  pub fn foo() -> () {
    // Instantiate the contract from contract id
    let bar = Bar(#15be9537456726c336a3cd1aa36074759c457f151ac253a500085920afe3838a)
    // External call
    let a = bar.func()
    // ...
  }
}
```

### 内置方法

Ralph provides lots of builtin functions, you can refer to [here](https://docs.alephium.org/ralph/built-in-functions)

## 合约

### 合约定义

Contracts in Ralph are similar to classes in object-oriented languages. Each contract can contain declarations of contract fields, events, constants, enums, and functions. All these declarations must be inside a contract. Furthermore, contracts can inherit from other contracts.

```rust
// This is a comment, and currently Ralph only supports line comments.
// Contract should be named in upper camel case.
// Contract fields are permanently stored in the contract storage.
Contract MyToken(supply: U256, name: ByteVec) {

  // Events should be named in upper camel case.
  // Events allow for logging of activities on the blockchain.
  // Applications can listen to these events through the REST API of an Alephium client.
  event Transfer(to: Address, amount: U256)

  // Constant variables should be named in upper camel case.
  const Version = 0

  // Enums can be used to create a finite set of constant values.
  enum ErrorCodes {
    // Enum constants should be named in upper camel case.
    InvalidCaller = 0
  }

  // Functions, parameters, and local variables should be named in lower camel case.
  pub fn transferTo(toAddress: Address) -> () {
    let payloadId = #00
    // ...
  }
}
```

### 修饰符

合约属性默认为私有，若允许外界调用，pub 关键字用于修饰，且仅用于修饰方法。

### 枚举

```rust
enum ErrorCodes {
  InvalidCaller = 0
}
```

### 字段

Contract fields are permanently stored in the contract storage, and the fields can be changed by the contract code. Applications can get the contract fields through the REST API of an Alephium client.

```rust
// Contract `Foo` has two fields:
// `a`: immutable, it can not be changed by the contract code
// `b`: mutable, it can be changed by the contract code
Contract Foo(a: U256, mut b: Boolean) {
  // ...
}

// Contract fields can also be other contract.
// It will store the contract id of `Bar` in the contract storage of `Foo`.
Contract Foo(bar: Bar) {
  // ...
}

Contract Bar() {
  // ...
}
```

### event

Events are dispatched signals that contracts can fire. Applications can listen to these events through the REST API of an Alephium client.

```rust
Contract Token() {
  // The number of event fields cannot be greater than 8
  event Transfer(to: Address, amount: U256)

  @using(assetsInContract = true)
  pub fn transfer(to: Address) -> () {
    transferTokenFromSelf!(selfTokenId!(), to, 1)
    // Emit the event
    emit Transfer(to, 1)
  }
}
```

### Contract Creation inside a Contract

Ralph supports creating contracts programmatically within contracts, Ralph provides some builtin functions to create contracts, you can find more information at [here](/ralph/built-in-functions#contract-functions).

If you want to create multiple instances of a contract, then you should use the `copyCreateContract!` builtin functions, which will reduce a lot of on-chain storage and transaction gas fee.

```rust
Contract Foo(a: ByteVec, b: Address, c: U256) {
  // ...
}

// We want to create multiple instances of contract `Foo`.
// First we need to deploy a template contract of `Foo`, which contract id is `fooTemplateId`.
// Then we can use `copyCreateContract!` to create multiple instances.
TxScript CreateFoo(fooTemplateId: ByteVec, a: ByteVec, b: Address, c: U256) {
  let encodedFields = encodeToBytes!(a, b, c)
  copyCreateContract!(fooTemplateId, encodedFields)
}
```

## 接口

Interfaces are similar to abstract contracts with the following restrictions:

- They cannot have any functions implemented.
- They cannot inherit from other contracts, but they can inherit from other interfaces.
- They cannot declare contract fields.
- Contracts can only implements one interface.

```rust
Interface Foo {
  event E(a: U256)

  @using(assetsInContract = true)
  pub fn foo() -> ()
}

Interface Bar extends Foo {
  @using(readonly = true)
  pub fn bar() -> U256
}

Contract Baz() implements Bar {
  // The function signature must be the same as the function signature declared in the interface.
  @using(assetsInContract = true)
  pub fn foo() -> () {
    // Inherit the event from `Foo`
    emit E(0)
    // ...
  }

  @using(readonly = true)
  pub fn bar() -> U256 {
    // ...
  }
}
```

And you can instantiate a contract with interface:

```rust
let bazId = // The contract id of `Baz`
Foo(bazId).foo()
let _ = Bar(bazId).bar()
```

**注意**:
Deploying a contract requires depositing a certain amount of ALPH in the contract(currently 1 alph), so creating a large number of sub-contracts is not practical.

## 继承

Ralph also supports multiple inheritance, when a contract inherits from other contracts, only a single contract is created on the blockchain, and the code from all the parent contracts is compiled into the created contract.

```rust
Abstract Contract Foo(a: U256) {
  pub fn foo() -> () {
    // ...
  }
}

Abstract Contract Bar(b: ByteVec) {
  pub fn bar() -> () {
    // ...
  }
}

// The field name of the child contract must be the same as the field name of parnet contracts.
Contract Baz(a: U256, b: ByteVec) extends Foo(a), Bar(b) {
  pub fn baz() -> () {
    foo()
    bar()
  }
}
```

**note**：
In Ralph, abstract contracts are not instantiable, which means the following code is invalid:

```rust
let bazId = // The contract id of `Baz`
Foo(bazId).foo() // ERROR
```

## 脚本（执行入口）

A transaction script is a piece of code to interact with contracts on the blockchain. Transaction scripts can use the input assets of transactions in general. A script is disposable and will only be executed once along with the holder transaction.

```rust
Contract Foo() {
  pub fn foo(v: U256) -> () {
    // ...
  }
}

// The `preapprovedAssets` is true by default for `TxScript`.
// We set the `preapprovedAssets` to false because the script does not need assets.
@using(preapprovedAssets = false)
// `TxScript` fields are more like function parameters, and these
// fields need to be specified every time the script is executed.
TxScript Main(fooId: ByteVec) {
  // The body of `TxScript` consists of statements
  bar()
  Foo(fooId).foo(0)

  // You can also define functions in `TxScript`
  fn bar() -> () {
    // ...
  }
}
```

## 注解

### 方法注解

The Ralph function also supports annotations, currently the only valid annotation is the `@using` annotation, and user-defined annotations will be supported in the future if necessary.

The `@using` annotation has four optional fields:

- `preapprovedAssets = true/false`: whether the function uses user-approved assets. The default value is `false` for contracts, `true` for scripts.
- `assetsInContract = true/false`: whether the function uses contract assets. The default value is `false` for contracts
- `externalCallCheck = true/false`: whether the function checks the caller. The default value is `true` for contracts
- TODO

##### Using Approved Assets

In Ralph, if a function uses assets, then the caller needs to explicitly approve assets. And all functions in the call stack must be annotated with `@using(approvedAssets = true)`.

```rust
Contract Foo() {
  // Function `foo` uses approved assets, and it will transfer 1 ALPH and 1 token to the contract from the `caller`
  @using(approvedAssets = true)
  fn foo(caller: Address, tokenId: ByteVec) -> () {
    transferAlphToSelf!(caller, 1 alph)
    transferTokenToSelf!(caller, tokenId, 1)
  }

  @using(approvedAssets = true)
  fn bar(caller: Address, tokenId: ByteVec) -> () {
    // We need to explicitly approve assets when calling function `foo`
    foo{caller -> 1 alph, tokenId: 1}(caller, tokenId)
    // ...
  }
}
```

##### Using Contract Assets

```rust
Contract Foo() {
  // Function `foo` uses the contract assets, and it will transfer 1 alph to the caller
  @using(assetsInContract = true)
  fn foo(caller: Address) -> () {
    transferAlphFromSelf!(caler, 1 alph)
  }

  // Function `bar` must NOT be annotated with `@using(assetsInContract = true)`
  // because the contract assets will be removed after use
  fn bar(caller: Address) -> () {
    // ...
    foo(caller)
  }
}
```

You can find more information about asset permission at [here](/ralph/asset-permission-system).

##### External Call Check

In smart contracts, we often need to check whether the caller of the contract function is authorized. To avoid bugs caused by unauthorized callers, the compiler will report warnings for public functions that do not check for external calls. The warning can be suppressed with annotation `@using(externalCallCheck = false)`.

To check the caller of a function, the built-in function [checkCaller!](/ralph/built-in-functions#checkcaller) has to be used.

```rust
Contract Foo(barId: ByteVec, mut b: Boolean) {
  enum ErrorCodes {
    InvalidCaller = 0
  }

  // We don't need to add the `@using(externalCallCheck = true)` because
  // the `externalCallCheck` is true by default for public functions.
  pub fn f0() -> () {
    // The `checkCaller!` built-in function is used to check if the caller is valid.
    checkCaller!(callerContractId!() == barId, ErrorCodes.InvalidCaller)
    b = !b
    // ...
  }

  // Function `f1` is readonly, so it does not need to check the caller.
  // We need to explicitly add the `@using(externalCallCheck = false)` annotation.
  @using(externalCallCheck = false, readonly = true)
  pub fn f1() -> () {
    // ...
  }

  // The compiler will report warnings if the `f2` is called by other contract.
  pub fn f2() -> () {
    b = !b
    // ...
  }

  // The compiler will NOT report warnings if the `f3` is caller by
  // other contract, because we checked the caller in function`f4`.
  pub fn f3() -> () {
    f4(callerContractId!())
    // ...
  }

  fn f4(callerContractId: ByteVec) -> () {
    checkCaller!(callerContractId == barId, ErrorCodes.InvalidCaller)
    // ...
  }
}
```

### 字段或参数注解

TODO

## 类型转化

TODO

## Map

Ralph uses [subcontract](/ralph/getting-started#subcontract) instead of map-like data structure to provide map-like functionality and mitigate the state bloat issue.

## 错误处理

Ralph provides two builtin assertion functions for error handling: [assert!](/ralph/built-in-functions#assert) and [panic!](/ralph/built-in-functions#panic). Assertion failure will revert all changes made to the world state by the transaction and stop the execution of the transaction immediately.

```rust
enum ErrorCodes {
  InvalidContractState = 0
}

fn foo(cond: Boolean) -> () {
  // It will stop the transaction if `cond` is false.
  // The Alephium client will return the error code if the transaction fails.
  assert!(cond, ErrorCodes.InvalidContractState)
}

fn bar(cond: Boolean) -> U256 {
  if (!cond) {
    // The difference between `panic!` and `asset!` is that the return type of `panic!` is bottom type
    panic!(ErrorCodes.InvalidContractState)
  }
  return 0
}
```

## 调试方法

- debug!() 内置方法

TODO

## 特殊用法

TODO

## 编译器

```shell
java -jar alephium-ralphc-1.6.4.jar  -h

ralphc 1.6.4
Usage: ralphc [options]

Examples:
  $ java -jar ralphc.jar -c <contracts1>,<contracts2>... -a <artifacts1>,<artifacts2>... [options]

Options:
  -c, --contracts <value>  Contract folder, default: ./contracts
  -a, --artifacts <value>  Artifact folder, default: ./artifacts
  -w, --werror             Treat warnings as errors
  --ic                     Ignore unused constant warnings
  --iv                     Ignore unused variable warnings
  --if                     Ignore unused field warnings
  --ir                     Ignore update field check warnings
  --ip                     Ignore unused private functions warnings
  --ie                     Ignore check external caller warnings
  -d, --debug              Debug mode
  -h, --help               Print this usage text
  -v, --version            Print version information
```

## 编译

```shell
java -jar alephium-ralphc-1.6.4.jar -c <contracts>  -a <artifacts>
```

## 部署

TODO

## 工具

- [ralph-vscode](https://github.com/alephium/ralph-vscode)
- [ralphc](https://github.com/alephium/alephium)

## 样例

TODO

## 参考

- https://github.com/alephium/alephium

## Q&A

- 标准库：内置方法.
- 标准合约
