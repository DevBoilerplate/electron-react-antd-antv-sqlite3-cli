# electron-react-antd-antv-sqlite3-cli

一个集成electron、react、antd、antv、sqlite3的开发脚手架，便于快速搭建基于react开发的electron桌面应用，本项目只针对Windows环境

> 该项目是集成sqlite3数据库的`electron-react-antd-antv-cli`的衍生项目，在实际开发中遇到了自带打包数据库的问题和`sqlite3`包不能直接使用的问题，考虑到多平台编译的缘故，把项目抽离开。

## 环境依赖

* nodejs [未安装可参考](https://herberthe.gitee.io/2020/01/26/Nodejs%E5%AE%89%E8%A3%85%E8%8B%A5%E5%B9%B2%E9%97%AE%E9%A2%98%E9%81%BF%E5%9D%91/)
* node-gyp
* windows-build-tools

> 关于electron的镜像，electron7.x之后需要设置单独的镜像，不然会安装报错；根据官方文档，中国地区镜像为: `https://cdn.npm.taobao.org/dist/electron/`，我参考过很多的教程仅发现一篇编译成功并且叙述详细，所遇到的问题和解决方式和这篇帖子几乎完全相同 [electron+sqlite3的一起使用](https://blog.csdn.net/zoepriselife316/article/details/89954383)

在Windows的环境下命令为:

```shell
# 关于electron
npm config set ELECTRON_MIRROR="https://cdn.npm.taobao.org/dist/electron/"

# 验证设置
npm config ls

# 成功配置输出
# ELECTRON_MIRROR="https://cdn.npm.taobao.org/dist/electron/"


# 关于node-gyp和windows-build-tools
# 全局安装node-gyp
npm install node-gyp -g

# 全局安装windows-build-tools
npm install windows-build-tools -g

# 安装windows-build-tools的时候会自动检查安装python2.x这个依赖，之前安装node-sass的时候我碰到过这个问题，怕麻烦的话提前装个vs2015，其实只是需要一个visual C++ Build Tools而已。。。
```

> 上面`visual C++ Build Tools`可以参考这个同学的文章 [Microsoft Visual C++ Build Tools下载/解决Visual C++ 14.0 is required问题](https://blog.csdn.net/antizheng/article/details/88633912)，点击这直接下载安装 [Visual C++ Build Tools 2015](http://go.microsoft.com/fwlink/?LinkId=691126)，这个都是Windows环境下的问题，`electron-rebuild`这个依赖在Windows10下编译`sqlite3`是报错的

## 快速使用

* `clone` 仓库

```shell
# github
git clone https://github.com/HerbertHe/electron-react-antd-antv-cli.git

# gitee
git clone https://gitee.com/HerbertHe/electron-react-antd-antv-cli.git
```

* 进入仓库

* 删掉node_modules目录！

* 下载依赖，会花费一些时间请耐心等待

```shell
yarn install
```

* 命令行打开进入sqlite3所在的目录 `node_modules/sqlite3`

```shell
cd node_modules/sqlite3
```

* 编译sqlite3（只针对Windows系统）

> 根据你的实际需求更改下述的命令，我在node的脚本命令里已经提供了针对于本项目依赖的命令请参考执行，命令解析请参考下面的参考项目

```shell
# 32位操作系统
node-gyp rebuild --target=7.1.11 --arch=ia32 --target_platform=win32 --dist-url=https://atom.io/download/atom-shell --module_name=node_sqlite3 --module_path=../lib/binding/electron-v7.1.11-win32-ia32

# 64位操作系统
node-gyp rebuild --target=7.1.11 --arch=x64 --target_platform=darwin --dist-url=https://atom.io/download/atom-shell --module_name=node_sqlite3 --module_path=../lib/binding/electron-v7.1.11-darwin-x64
```

* 验证安装

```shell
yarn run ele:dev
```

## 关于项目

查看项目从`package.json`开始，项目的`README.md`可能更新不及时

1. 已提供的指令

    ```shell
        "react:dev": "启动react的预览",
        "react:build": "构建react的产物",
        "react:test":  "react测试",
        "react:eject": "react-scripts eject",
        "ele:dev": "启动electron开发预览"
        # 下面详述包及自定义配置，目前只针对electron的开发做了脚本
    ```

    ```shell
        # 项目启动后在任何时间都可以在命令行输入rs回车使用nodemon强制项目更新
        rs
        # Ctrl + c后输入y即可退出命令行启动预览环境
    ```

2. 项目的默认包管理

    本项目默认使用`yarn`进行包管理

3. 项目依赖包详解

    * 项目中使用的开发依赖`react-app-rewired` `customize-cra` `babel-plugin-import` `less` `less-loader` `antd-dayjs-webpack-plugin` 全部基于 `antd`的[高级配置](https://ant.design/docs/react/use-with-create-react-app-cn)，没有使用`eject`对外暴露`webpack`来修改配置，使用了`react-app-rewired`的解决方案。`react-app-rewired`依赖`customize-cra`，对于自定义的webpack设置可以参考 [api](https://github.com/arackaf/customize-cra/blob/master/api.md) 获取使用所有`customize-cra`提供的API。配置文件为根目录的`config-overrides.js`

    * 项目的`dev`指令详解：`package.json`中对应指令为`nodemon --watch main.js --exec concurrently \"wait-on http://localhost:3000 && electron .\" \"cross-env BROWSER=none yarn start\"`，其中利用`nodemon`依赖监听`main.js`文件修改重载指令；`concurrently`并行`wait-on http://localhost:3000 && electron .`和`cross-env BROWSER=none yarn start`指令；`wait-on`依赖用于监听本地react服务的启动情况，当本地加载完成时执行`electron .`；`cross-env`利用了交叉环境设置环境变量`BROWSER=none`从而避免react自动启动浏览器预览

    * 项目自带了`antd`和`bizCharts`，提供了样式库和图形可视化的能力，`bizCharts`是阿里基于antv g2的React商业化封装，具体文档可参考下面的文档引用

## 参考文档

* [在`create-react-app`中使用](https://ant.design/docs/react/use-with-create-react-app-cn)
* [react-app-rewired](https://github.com/timarney/react-app-rewired#alternatives)
* [customize-cra](https://github.com/arackaf/customize-cra)
* [bizCharts](https://bizcharts.net/index)
* [Ant Design of React](https://ant.design/docs/react/introduce-cn)
* [electron](https://www.electronjs.org/docs)
