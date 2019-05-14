# Swoole-Grpc-Client

[![Latest Version](https://img.shields.io/github/release/swoole/grpc-client.svg?style=flat-square)](https://github.com/swoole/grpc-client/releases)
[![Build Status](https://travis-ci.org/swoole/grpc-client.svg?branch=master)](https://travis-ci.org/swoole/grpc-client)
[![Php Version](https://img.shields.io/badge/php-%3E=7-brightgreen.svg?maxAge=2592000)](https://secure.php.net/)
[![Swoole Version](https://img.shields.io/badge/swoole-%3E=4.0.4-brightgreen.svg?maxAge=2592000)](https://github.com/swoole/swoole-src)
[![Swoole License](https://img.shields.io/hexpm/l/plug.svg?maxAge=2592000)](https://github.com/swoole/grpc-client/blob/master/LICENSE)

## Introduction

由Swoole驱动的Grpc协程客户端, 底层使用高性能协程Http2-Client客户端

- 同步代码几乎无改动
	- 自动**协程调度获得异步高性能**
	- 提供Grpc代码生成器Plus版, 0成本迁移
- 基于Channel实现的消息生产消费
  - **一个客户端连接**即可**同时**hold住**上万个**请求响应
  - 支持跨协程并发, 多类型Client分享同一连接

<br>

## Requirement

- PHP7及以上
- [Swoole](https://github.com/swoole/swoole-src): 4.0.4及以上
- [Protobuf](https://github.com/google/protobuf/tree/master/php)
- [grpc_php_plugin](https://github.com/grpc/grpc/)
- 请不要启用grpc的php扩展, 也无需grpc的php库
- 请不要启用protobuf的php扩展, 也无需protobuf的php库

<br>

## Usage

仓库已提供Etcd的生成代码, 如要自己根据proto文件生成代码, 请使用`tools`目录下的生成工具`generator`, 使用方法和`protocal`命令完全一样, 增强了支持以目录作为参数, 自动查找目录下的proto文件生成, 如: 该目录下已提供的`grpc`生成代码脚本:

```shell
# it's generate_grpc.sh
./generator \
--proto_path=./../src/Grpc/Proto \
--php_out=./../src/Grpc \
--grpc_out=./../src/Grpc \
--grpc_php_out=./src/Grpc \
--plugin=protoc-gen-grpc=$1 \
./../src/Grpc/Proto
```

只需要将proto文件放在`Grpc/Proto`下, 运行`./generate_grpc.sh ../../grpc/bins/opt/grpc_php_plugin` (参数是你的grpc php插件位置, 一般在`grpc/bins/opt`目录中), 即可生成相关代码

<br>

生产的文件目录结构如下

```
out
├── GPBMetadata
│   └── Helloworld.php
└── Helloworld
    ├── GreeterClient.php           -- php client for service
    ├── GreeterInterface.php        -- php service for service
    ├── GreeterService.php          -- php service for service
    ├── HelloReply.php              -- request message class generated by protoc
    └── HelloRequest.php            -- response message class generated by protoc
```

##  Examples

>  以下示例都可在`examples`目录下找到并直接运行

### Grpc

---

#### HelloWorld

经典的Grpc官方示例, 代码更加简洁

```php
$greeterClient = new GreeterClient('127.0.0.1:50051');
$greeterClient->start();
$request = new HelloRequest();
$request->setName('Swoole');
list($reply, $status) = $greeterClient->SayHello($request);
$message = $reply->getMessage();
echo "{$message}\n"; // Output: Hello Swoole
```
