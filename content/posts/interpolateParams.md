---
title: "go mysql driver InterpolateParams"
date: 2022-05-24T11:00:22+08:00
draft: false
slug: go-mysql-driver-InterpolateParams
---

## 背景
在参加字节的青训营的jinzhu老师讲述的database sql 与 GORM 实践时讲到了go-sql-driver/mysql 的 DSN 的一个叫 interpolateParams 的prameters，jinzhu老师讲到他的开关对性能有很大影响，该参数涉及到预编译等内容，这正好是我上次面试时打错的地方，真好引起我的兴趣来探究一下它。

## [参数介绍](https://github.com/Go-SQL-Driver/MySQL/#interpolateparams)

根据github readme里面的介绍当使用interpolateParams参数（即interpolateParams = true，默认为true）时，调用[db.query()](https://pkg.go.dev/database/sql#DB.Exec)和[db.call()](https://pkg.go.dev/database/sql#DB.Query)方法时，query中的占位符(?)会被args所替换，生成一条sql语句，client只需发送这条语句给server执行即可。

如果关闭这一参数，db.call()和db.query()的执行会分成3步，第一步是client发送query给server来预编译，第二步client发送args给server用于执行预编译的语句，第三步关闭预编译语句。

readme里面说得很清楚使用这个参数会减少传输的RTT。

## 参数效果

我并没有对这个参数进行测试，但是网上已经大佬测试过了，详见这篇[博文](https://wklken.me/posts/2021/01/22/golang-sql-driver-interpolateparams.html)，可以看到性能提升是巨大的。

## 对于预编译是否有影响

这个参数开启对于DB.Prepare等预编译相关的方法是无影响的，详见这个[issue](https://github.com/go-sql-driver/mysql/issues/413)。

## 总结

通过这个参数对于go mysql driver包的sql执行过程有了更加深入的了解，顺便复习了预编译的内容。