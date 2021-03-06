# 工作相关
## 登陆验证
token 验证 作为装饰器统一验证，验证通过后返回主逻辑
## 接受参数，返回结果，异常处理
由于数据比较统一，统一处理参数，和返回结果，有意想不到的惊喜

## swagger
丝袜哥，对url，请求参数，返回结果都有好的生成记录，希望自己对`restful`此装饰器进行统一的添加。

## 假设
任何公司都需要盈利。
应用架构
对单表数据进行抽象提供对系统级别的接口
delete 各个版本不同
好像又一种有意思的东西，哈哈，简直有意思
django 内部的路由，
前后分离的问题
将django 做成flask风格的代码，并不是什么好的创意，可能实现的方法还有问题，因为用了最简单的 方法
有时候业务和技术是密不可分的，单单脱离了什么都会缺少什么。

## 源码阅读
老师教你的也就是入门，然后剩下的就是领悟了，绘画无非是各大美术馆，而各大开源项目就像是巨大的美术馆。
对oauth 的阅读。待讨论中四种模式，标准模式，简k模式，密码模式，客户端模式
其中客户端模式更像是api调用。密码模式更像是代理。
使用soap但是又不完全使用。
不过将 django 中集成如 pysimplesoap 也还不错。再做一下 dispatch 基本上就满足要求了。
想将pysimplesoap 这个框架集成方式开源出来。
而且将刨析oauth这个方式写一下。 
很多收旧应该使用订阅模式解耦然而并没有
过分的依赖cbv会有意想不到的 bug，当然写起来很爽，其实也不算是bug吧，只不过需要每次需要初始化一下公共变量
在看pysimplesoap 的源码

## websockets
使用uwsgi+django 启动的websocket长链接就会阻塞线程，如果不使用异步就会导致进程阻塞
最终使用了 eventlet 启动wsgi 应用，在此作笔记
使用 uwsgi 启动eventlet进程后，并发一直撑到750 进程挂掉了，然后nginx 不再转发websocket 内容，即websocket不再请求页面，导致效率极低
真的 eventlet 后撑到1300 的客户端，还没找到什么好方法

也不知道写点什么，就当牢骚一点吧，最近工作还可以。
搭建了 jupyter 环境，但是还没想好怎么用。

用于user登陆登出的问题
由于现在已经耦合了11个项目了。简直不敢大改了
只好做出 前端模拟登出，然后删掉headers的方案来模拟登出

除了更改表结构，目前还没想到有什么 能灵活满足需求的登陆方式，而且很多老项目柔和在一起，想想就很恐怖了。

同样又考虑了 项目之间的柔和，之前的问题，貌似可以 提供不同的登陆方式，例如token和session 的登陆，不过对之前项目影响很大，还是采取比较小的方式。
现在写代码之前，要考虑之前项目的影响，和新项目的规模，希望都在掌握之中吧。
我想websocket 出了一点问题，看来线程对于python还是不要说了吧
还有个kafka的问题，之前一直在用线程来跑的循环，现在想想也是隐患吧

websocket 到现在应该算是一段落了，如果还有问题的话，只好推迟了

接手前使用，单进程，单线程跑
导致在维持长连接的时候阻塞进程，导致其他页面不能访问

使用多线程
在更换协程（eventlet）启动后不存在进程阻塞了
线程主要用于发布kafka 的消费者

问题就是 线程之间的共用内存，和gil 的问题，导致有时会锁死在单个线程，其他线程不存在切换，导致有些消息不能及时发布

使用多进程
在寻求帮助之后，发现问题在于kafka的进程阻塞，两种解决办法，第一种使用kafka的异步队列，订阅其消息，然后进行回调
第二种，就是将发布写成服务，一旦有订阅，会请求此服务，避免了同一个项目多个线程的问题。
现阶段启用第二种方式，希望稳定性会很好

## kafka
之前一直没怎么用过消息队列，用起来其实很方便，应该是因为项目比较小，所以很难遇到性能的瓶颈吧

奇怪的问题，group id 有一个组居然总是会拿到历史数据，过了一天之后，有莫名其妙的好了。
同样的问题有复现，却不知道怎么复现的 这样的问题就同样出现了。

在特殊情况下 get_manager_id 会返回一个 none 然后会导致程序崩溃

## nginx
有一个接口响应慢，拖慢了整个进程，然后导致一直报 broken pipe error 32 这样的 错误。

## mysql
很多情况下 因为sql导致语句执行慢，拖慢整个接口速度

## django

针对多表回滚，django也有
```python
from django.db import transaction
```

## 代码风格
 现在，代码的风格是一个很玄妙的东西，在函数入口我认为 将局部变量声明比较好，虽然python是一种很灵活的语言，可以容许你可以在使用的时候任意声明，但是也造成了一定的困扰。

## gevent

询问朋友之后，说是不能 位置loop 只能保证 其不阻塞

## 周报
我 觉得我还是需要一个 周报自动生成的 东西，虽然有了 trello 和 tower ，但是 生成的 太烂了。

## api service
在上一个新技术的前提就是对他做好调研，重要的是要解决什么问题，一定要找到问题所在，并非听说了一门新技术就要上。就这么简单
还没有拆分，只是进行了 简单的归类，就已经划分出9个项目，如果需要拆分部署，不知道工作量是多大。