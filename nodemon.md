# nodemon使用简介

([nodemon Git地址](https://github.com/remy/nodemon#nodemon))

### 什么是nodemon
nodemon用来监视node.js应用程序中的任何更改并自动重启服务,非常适合用在开发环境中。
nodemon将监视启动目录中的文件，如果有任何文件更改，nodemon将自动重新启动node应用程序。
nodemon不需要对代码或开发方式进行任何更改。 nodemon只是简单的包装你的node应用程序，并监控任何已经改变的文件。nodemon只是node的替换包，只是在运行脚本时将其替换命令行上的node。

### 使用教程
1. 全局安装

```
npm install -g nodemon
```

2. 本地安装

```
npm install --save-dev nodemon
```

3. 启动应用

```
nodemon [your node app]
```

4. 使用帮助
```
nodemon -h  或者  nodemon --help
```
5. 如果没有在应用中指定主机和端口，可以在命令中指定:

```
nodemon ./server.js localhost 8080
```

6. 开启debug模式
```
nodemon --debug ./server.js 80
```







