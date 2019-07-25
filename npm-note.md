# NPM学习笔记

## nrm - NPM registry 管理工具

1. 浏览当前 NPM 源
```bash
 $ nrm ls

 * npm ---- https://registry.npmjs.org/
   cnpm --- http://r.cnpmjs.org/
   eu ----- http://registry.npmjs.eu/
   au ----- http://registry.npmjs.org.au/
   sl ----- http://npm.strongloop.com/
   nj ----- https://registry.nodejitsu.com/
```

2. 切换 NPM 仓库

```bash
$ nrm use cnpm 
```

3. 增加源

```bash
$ nrm add qihoo http://registry.npm.360.org  # qihoo 为别名，后面为仓库地址
```

* 注：私有源有时无法安装部分包时，可切换至其他公共源再安装。

4. 删除源

```bash
$ nrm del qihoo
```

5. 测试源的速度

```bash
$ nrm test npm
```



## NPM 模块开发

在本地开发 npm 模块的时候，我们可以使用`npm link`命令，将 npm 模块链接到对应的运行项目中去，方便地对模块进行调试和测试。

```bash
$ cd link-module # 进入模块目录
$ npm link # 将该模块链接到全局
$ cd ../link-project # 进入到项目目录
$ npm link link-module # 将模块链接到该项目，此时可方便进行模块开发的调试工作
```

