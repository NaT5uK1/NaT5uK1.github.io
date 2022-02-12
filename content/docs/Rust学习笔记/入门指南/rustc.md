---
title: rustc
toc: true
authors: []
date: 2022-02-12T17:55:52+08:00
lastmod: 2022-02-12T17:55:52+08:00
draft: false
weight: 2

---



## rustc是什么？

`rustc`是Rust语言的编译器，类似C语言的gcc，负责将*.rs程序编译为二进制文件



## 查看Rust版本

```shell
rustc --version
#或
rustc -V
```

因为Rust版本与rustc是高度一致的，语言特性与编译器是共生关系，所以rustc的版本即Rust本身的版本



## 运行一个Rust程序

新建`hello_world.rs`文件：

> Rust的文件/目录命名规范：全部小写字母，多个单词用下划线连接

```rust
fn main() {
    println!("hello world!");
}
```

将`hello_world.rs`文件编译为二进制文件：

```shell
rustc hello_world.rs
```

默认会生成无扩展名的同名二进制文件`hello_world`，执行：

```shell
./hello_world
```

应输出：

```shell
hello world!
```



## 使用场景

rustc只适用于简单的Rust应用，在复杂项目中使用[`cargo`](../cargo)会获得更佳的体验