---
title: 使用 Verdaccio 私有化 npm 源指南
zhihu-title-image: https://pic4.zhimg.com/80/v2-92014b8d2bcf1cde9d676168e852d1e9.png
zhihu-url: https://zhuanlan.zhihu.com/p/692416766
---

# 使用 Verdaccio 私有化 npm 源指南

- [使用 Verdaccio 私有化 npm 源指南](#使用-verdaccio-私有化-npm-源指南)
  - [介绍](#介绍)
  - [什么是 Verdaccio](#什么是-verdaccio)
  - [为什么选择 Verdaccio](#为什么选择-verdaccio)
  - [部署 Verdaccio](#部署-verdaccio)
    - [Nodejs 部署](#nodejs-部署)
      - [全局](#全局)
      - [局部](#局部)
    - [Docker 部署](#docker-部署)
    - [云服务商一键部署](#云服务商一键部署)
  - [注册用户](#注册用户)
  - [发布私有 npm 包](#发布私有-npm-包)
  - [管理 npm 包](#管理-npm-包)
  - [项目使用私有源](#项目使用私有源)
    - [全量切换](#全量切换)
    - [部分切换](#部分切换)
  - [结尾](#结尾)
  - [源代码链接](#源代码链接)

## 介绍

在日常的工作开发中，我们常常会遇到这样的场景:

公司或团队内部，需要开发和共享一些 逻辑/UI 组件等等的代码，

但是这些代码里面，可能包含一些敏感信息或专有功能，我们不想发布到公开的 `npmjs` 上去。

这时候我们就需要去搭建 `npm` 私有源，让这些包只在我们公司内部分享。

那这时候 `Verdaccio` 就是一个方便的选择。

## 什么是 Verdaccio

`Verdaccio` 是一个轻量级的私有 `npm` 仓库管理工具，它可以帮助我们搭建自己的私有 npm 源。

在这种情况下，使用 `Verdaccio` 搭建私有 `npm` 源可以提供以下优势：

- 安全性：通过搭建私有 npm 源，您可以更好地控制和保护自己的代码。只有授权用户能够访问您的私有源，防止未经授权的人员获取敏感信息或篡改包。

- 灵活性：通过自定义配置 `Verdaccio`，您可以根据团队或公司的需求来管理包和版本发布。您可以设置访问权限、存储路径等参数，以满足特定项目的要求。

- 提高效率：拥有一个专门为团队开发定制的私有 `npm` 源意味着团队成员之间能够更快速地共享和管理代码。不需要依赖公共 `npm` 仓库下载、安装包，节省了时间和带宽消耗。

- 自定义功能：`Verdaccio` 支持插件机制，允许您扩展其功能，并集成其他服务（如 `LDAP` 或 `GitHub` ）进行身份验证。这样就可以与现有系统集成，并实现更多定制化功能。

总而言之，在团队开发或者公司内部项目中使用 `Verdaccio` 私有化 `npm` 源是一种有效且灵活的方式来管理自定义包，并确保代码安全性与可靠性。通过搭建私有源, 团队能够更好地协作并加快项目进度。

## 为什么选择 Verdaccio

其实 `npm` 私有源目前开源主流，只剩下了 `Verdaccio`，`Nexus` 这 2 个项目，还在继续维护，更新频率也高

作为一个这 `2` 个项目，都用过的开发者，为什么我更推荐 `Verdaccio` 呢？

1. 首先，它的社区生态生态更好，插件层出不穷；而 `Nexus` 的开源版本就相形见绌不少（收费版没用过不知道）
2. 其次，它是 `js` 写的，我们前端开发者，可以很方便的编写插件，或者对它的源代码进行修改，添加我们自己的逻辑；而 `Nexus` 是 `java` 写的，需要一些学习成本
3. 再来，它的使用，以及包的发布，管理，预览方式非常像 `npmjs`，熟悉 `npmjs` 的使用它简直 `0` 成本；而 `Nexus` 界面，对包信息的展示，简直可以用简陋来形容。
4. 最后，其实它们 2 者关于 身份验证，权限控制 等等的功能都大差不差的。

当然有人肯定也会说，`Nexus` 多好啊，部署一份可以支持 `maven`, `npm`, `docker`, `pypi` ... 这么多跨语言的团队，同时都可以使用呢！

诚然，假如你是一个 `java` 或者 `运维人员`，这样想是完全没有问题的。

但是，假如你是一名 `前端/nodejs`，在有主导权的情况下，我建议你选择 `Verdaccio` ，

因为其实只有我们前端自己，最懂自己的使用需求。

`Nexus` 那种通用类型的私有包管理项目，注定照顾不了，每一个细分语言最佳的使用体验！

## 部署 Verdaccio

`Verdaccio` 的部署方式很多，都非常的简单，5 分钟内快速上手

### Nodejs 部署

#### 全局

安装好 `lts` 的 `nodejs` 之后，直接全局安装:

```sh
# npm
npm i -g verdaccio
# yarn
yarn global add verdaccio
# pnpm
pnpm i -g verdaccio
```

这时候，一个 `verdaccio` 的命令会被注册在全局

执行 `verdaccio -v` 查看版本，显示版本号，代表你安装成功！

然后执行 `verdaccio`，你的私有源就在本地顺利启动了，启动日志如下所示。

```bash
 info --- config file  - /Users/icebreaker/.config/verdaccio/config.yaml
 info --- the "crypt" algorithm is deprecated consider switch to "bcrypt" in the configuration file. Read the documentation for additional details
 info --- using htpasswd file: /Users/icebreaker/.config/verdaccio/htpasswd
 info --- plugin successfully loaded: verdaccio-htpasswd
 info --- plugin successfully loaded: verdaccio-audit
 warn --- http address - http://localhost:4873/ - verdaccio/5.30.3
```

从启动日志中可以提取出几个重要信息:

1. 加载配置的位置，默认情况下会在全局用户文件夹里，初始化一个 `config` 文件
2. 用户名密码 hash 的存储文件 `htpasswd` 的位置
3. 加载的插件信息
4. 监听的 ip 和端口，默认是 `localhost:4873`，所以这时候是无法被其他机器访问到的

想要被其他机器访问到，需要去修改 `config.yaml` 这样反注释修改端口:

```yml
listen:
  - 0.0.0.0:4873
```

这样就能被内网里的其他机器访问到了，当然前提你要先开放(添加)你机器的端口出入规则

#### 局部

同样我们可以创建文件夹，在里面初始化一个 `package.json` 文件

然后依次创建 `config.yaml` 并添加 `start` 命令即可:

```json
{
  "name": "npm-proxy-verdaccio",
  "scripts": {
    "start": "verdaccio --config ./config.yaml"
  },
  "dependencies": {
    "verdaccio": "^5.30.3"
  }
}
```

### Docker 部署

在安装 `docker` 之后，我们可以创建一个 `docker-compose.yml`:

```yaml
version: "3.1"
name: npm-proxy-verdaccio

services:
  verdaccio:
    image: verdaccio/verdaccio
    container_name: "verdaccio"
    networks:
      - node-network
    environment:
      - VERDACCIO_PORT=4873
    ports:
      - "4873:4873"
    volumes:
      - "./storage:/verdaccio/storage"
      - "./config:/verdaccio/conf"
      - "./plugins:/verdaccio/plugins"
networks:
  node-network:
    driver: bridge
```

然后使用 `docker compose up -d` 就能顺利启动了, 不过这种方式需要自己准备 `config.yaml` 文件放入容器卷中。

### 云服务商一键部署

这种主要是国外的云服务商支持，我们用的比较少，详见：

<https://verdaccio.org/docs/installation#cloudron>

## 注册用户

部署完了之后我们开始注册用户，打开本机的 `http://0.0.0.0:4873/` 可以看到页面

![Image](https://pic4.zhimg.com/80/v2-84d9e39f3127485128fc1bc0000b56d6.png)

我们本地执行 `npm adduser --registry http://0.0.0.0:4873/`

按照顺序，输入我们的用户名密码邮箱

完成之后， `verdaccio` 目录下会出现一个 `htpasswd` 文件

里面格式如下:

```txt
icebreaker:xxxxxx:autocreated 2024-04-08T14:23:03.152Z
```

同时你全局的 `.npmrc` 里面出现:

```txt
//0.0.0.0:4873/:_authToken="xxxxxxx"
```

这代表你可以发包了！

## 发布私有 npm 包

发布私有 npm 包可以很简单，也可以很复杂(各种编译)

这里我们按照简单的来, 创建一个 `test-pkg` 文件夹，初始化 `package.json`

```json
{
  "name": "test-pkg",
  "version": "0.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {},
  "keywords": [],
  "files": ["index.js"],
  "author": "",
  "license": "ISC",
  "publishConfig": {
    "access": "public",
    "registry": "http://0.0.0.0:4873/"
  }
}
```

然后随便创建个 `index.js` 写点啥，就可以直接在项目里执行 `npm publish` 了

发布成功之后可以看到，我们的包已经上传上去了!

![Image](https://pic4.zhimg.com/80/v2-32b0c7d6a73610062415218b6f69d993.png)

点击进入详情：

![Image](https://pic4.zhimg.com/80/v2-f9b4d92e844bac3016c7d08a477d3bb6.png)

可以看到，界面元素和 `npmjs` 也差不多，也是可以查看 `README.md` 和各种版本依赖的。

## 管理 npm 包

此时我们上传 npm 包后，相关的文件就被上传到 `config.yaml` 里配置的 `storage` 里去了

这里我们配置的是 `storage: ./storage`，所以所有包的位置都在这

![Image](https://pic4.zhimg.com/80/v2-5a50f7647c9d5451104d47c4c5272e96.png)

在这里我们就能对包进行删除等等操作了，比如想让某个包在列表里看不见，直接把它在 `.verdaccio-db.json` 文件里删去即可。

## 项目使用私有源

默认情况下，`Verdaccio` 是开启 `proxy` `npmjs` 模式的

这个意思就是，你向它请求安装的包，假如 `Verdaccio` 里面没有的话，会去 `npmjs` 上找，然后返回给用户，同时也放在 `Verdaccio` 服务器本地缓存下来，这样下次同样的包就会命中缓存。

但是同样，这也会带来一些问题，比如全量代理之后，服务器 SSD 容量够不够大，需不需要定期清理那些用不上的 `npm` 包的问题。这种看各个团队/运维的需求和能力，所以项目里一般也是分为 `2` 种方式去使用它。

### 全量切换

顾名思义，全部的 `npm` 包都从 `Verdaccio` 服务器获取。

方式也很简单，只需要在 `.npmrc` 里添加:

```txt
registry=http://0.0.0.0:4873/
```

然后删掉 `lock` 文件，重新安装即可

### 部分切换

显式指明我们自己的包，在 `Verdaccio` npm 私有仓库中的的完整路径。（指明 tgz 包的版本）

```json
// package.json
{
  "dependencies": {
    "xxx": "http://xxxx"
  }
}
```

或者，可以在在 `.npmrc` 里显式指定特定包的特定注册源

```sh
xxxx:registry=http://xxxx
# @scoped/xxx
@xxx:registry=http://xxxx
```

这样我们 `package.json` 里面就不会出现这种不和谐( `http:` / `npm:` / `git:`)的版本号了。

## 结尾

写到这里，发现文字写的太多了，太长的文章没人看，所以这篇文章就到此为止。

但是实际上 `Verdaccio` 还有很多强大的功能，推荐大家去尝试尝试。

## 源代码链接

<https://github.com/sonofmagic/npm-proxy-verdaccio>
