---
title: cargo
description:
toc: true
authors: []
date: 2022-02-12T18:34:44+08:00
lastmod: 2022-02-12T18:34:44+08:00
draft: false
weight: 3

---

## 什么是cargo?

`cargo`是Rust的**构建工具**和**包管理工具**



## 查看cargo版本

```shell
cargo --version
```



## 使用cargo new创建项目

```shell
cargo new hello_cargo
```

得到如下目录结构：

```
hello_cargo
├── .git
│   ├── HEAD
│   ├── config
│   ├── description
│   ├── hooks
│   │   └── README.sample
│   ├── info
│   │   └── exclude
│   ├── objects
│   │   ├── info
│   │   └── pack
│   └── refs
│       ├── heads
│       └── tags
├── .gitignore
├── Cargo.toml
└── src
    └── main.rs
```

可以看出，`cargo new`做了以下工作：

1. 创建了一个与项目名同名的文件夹`hello_cargo`

2. 在项目根目录下新建了一个名为`Cargo.toml`的配置文件

3. 在项目根目录下创建了`src`文件夹，其中含有`main.rs`文件，`main.rs`文件预置了main函数作为项目的入口

4. 初始化了git仓库，并配置了`.gitignore`文件



### Cargo.toml

默认会生成如下内容：

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2021"

[dependencies]

```

* `[package]`，项目的基本配置
  + `name`，项目名
  + `version`，项目版本
  + `edition`，使用的Rust版本
* `[dependencies]`，项目的依赖项，依赖的包称作`crate`

+ 其他配置项详见[The Cargo Book](https://doc.rust-lang.org/cargo/reference/manifest.html#the-manifest-format)



## 使用cargo build构建项目

```shell
cd hello_cargo
```



### 开发环境下构建

```shell
cargo build
```

得到如下目录结构：

```
hello_cargo
├── Cargo.lock
├── Cargo.toml
├── src
│   └── main.rs
└── target
    ├── CACHEDIR.TAG
    └── debug
        ├── build
        ├── deps
        ├── examples
        ├── hello_cargo
        ├── hello_cargo.d
        └── incremental
```

可以看出，`cargo build`做了以下工作：

1. 在根目录生成`Cargo.lock`文件，负责追踪项目依赖的精确版本，类似于yarn中`yarn.lock`的作用
2. 在`target/debug/`目录下生成可执行文件`hello_cargo`



### 生产环境下构建

```shell
cargo build --release
```

得到如下目录结构：

```shell
hello_cargo
├── Cargo.lock
├── Cargo.toml
├── src
│   └── main.rs
└── target
    ├── CACHEDIR.TAG
    ├── debug
    │   ├── build
    │   ├── deps
    │   ├── examples
    │   ├── hello_cargo
    │   ├── hello_cargo.d
    │   └── incremental
    └── release
        ├── build
        ├── deps
        ├── examples
        ├── hello_cargo
        ├── hello_cargo.d
        └── incremental
```

与开发环境下相比：

1. 编译时会进行优化，代码会运行得更快，编译时间更长

2. 会在`target/release/`目录下生成可执行文件`hello_cargo`



## 使用cargo run运行项目

```shell
cargo run
```

得到如下输出：

```shell
   Compiling hello_cargo v0.1.0 (*/**/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.29s
     Running `target/debug/hello_cargo`
Hello, world!
```

`cargo run`是**编译代码**+**执行二进制文件**两个过程，即可以不借助`cargo build`完成项目运行

> 如果之前编译成功过（`cargo build`或者`cargo run`)，并且源码没有更改，则会直接运行二进制文件



## 使用cargo check检查代码

```shell
cargo check
```

得到如下输出：

```shell
    Finished dev [unoptimized + debuginfo] target(s) in 0.03s
```

代表代码通过编译

> 此过程不会产生任何可执行文件，速度远比`cargo build`快