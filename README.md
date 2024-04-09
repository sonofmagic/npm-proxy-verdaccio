# 使用 verdaccio 私有化 npm 源指南

- [使用 verdaccio 私有化 npm 源指南](#使用-verdaccio-私有化-npm-源指南)
  - [项目的安装和搭建](#项目的安装和搭建)
  - [切换到私有源](#切换到私有源)
    - [全量切换](#全量切换)
    - [部分切换](#部分切换)

## 项目的安装和搭建

执行 `npm adduser --registry http://0.0.0.0:4873/`

按照顺序完成之后，会出现一个 `htpasswd` 文件

## 切换到私有源

### 全量切换

在 `.npmrc` 里添加

```txt
registry=http://registry.npmmirror.com/
```

然后重新安装即可，可能需要删掉 `lock` 文件

### 部分切换

显式指明包在npm私有仓库中的的完整路径，（指明tgz包的版本）

```json
// package.json
{
   "dependencies": {
     "xxx": "http://xxxx"
   }
}
```

或者，可以在在 `.npmrc` 里显式指定特定包的特定注册源

```txt
xxxx:registry=http://xxxx
```
