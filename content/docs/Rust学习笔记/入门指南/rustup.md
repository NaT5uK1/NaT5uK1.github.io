---
title: rustup
toc: true
authors: []
date: 2022-02-12T13:53:35+08:00
lastmod: 2022-02-12T13:53:35+08:00
draft: false
weight: 1
---



## rustup是什么？

> `rustup` 用于管理不同平台下的 Rust 构建版本并使其互相兼容， 支持安装由 Beta 和 Nightly 频道发布的版本，并支持其他用于交叉编译的编译版本。

`rustup`是Rust的**工具链管理工具**



## 安装Rust

```shell
curl https://sh.rustup.rs -sSf | sh
```

使用上述命令后，会安装rustup，并自动运行Rust安装程序



## 更新Rust

```shell
rustup update
```



## 卸载Rust

```shell
rustup self uninstall
```



## 查看Rust本地文档

```shell
rustup doc
```