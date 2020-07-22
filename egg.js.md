# egg.js

## 个人见解

​		一切都是的命名要求，和文件的创建，都有一定的约束，相比Express针对一个router做一个处理来说。**egg将处理的过程，拆分出Controller和Service，在配合上全局的ctx和config，是过程简洁明朗。**egg的中间件和插件的挂载也是很有趣

## 全局的文件夹的创建

![image-20200722133740174](C:\Users\donsonwu\AppData\Roaming\Typora\typora-user-images\image-20200722133740174.png)

## 获取http请求

this.ctx.request.body

this.ctx.query.XXX

this.ctx.params.XX

## Config的配置和作用

默认配置，插件配置，中间件配置，参数配置，都可以在这个部分。

## Controller

在app文件下创建了controller，先写一个最简单的Controller

```js
const Controller = require('egg').Controller
class HomeController extends Controller {
	async index () {
	
	}
}

module.exports = HomeController
```

在routerjs中调用

```js
module.exports=app=>{
	const {router,controller}=app
	router.get('/',controller.home.index)  //根据文件夹的层级  controller/home/index
} 
```

## Service

在Service中，在复杂的业务逻辑中，做一个业务逻辑层的抽象。保证自己的Controller中的逻辑清晰

使用场景，更多的是在第三方服务的调用，和数据库的交互上

```
const Service = require('egg').Service
class HomeService extends Service {
	async find (uid){
		// 参数从controller中的传过来
		return  res
	}
}
module.exports = HomeService
```

在Controller中的调用   this.ctx.service.home.find()   //括号是执行函数

**发送请求   this.ctx.curl()**具体的看HttpClient部分

Service 不是单例，是 **请求级别** 的对象，框架在每次请求中首次访问 `ctx.service.xx` 时延迟实例化，所以 Service 中可以通过 this.ctx 获取到当前请求的上下文。

## 中间件

层次分明的层级类型，也可以针对**单独的路由**做一个适用

写一个中间件

```js
/app/middleware/robot.js
module.exports = (options,next) => {
	return async function (ctx,next){
		//do something
	await next()
	} 
}
```

需要在配置中配置中间件

```
/config/config.default.js
exports.middleware=['robot']//按照顺序匹配

//配置选项
exports.rebot={
	name:'xixi'
}

```



## 插件

一些无法使用中间件完成的功能，如和数据库进行交互。

/config/plugin.js

先申明插件

```、
exports.mysql={
	enable:true,
	package:'egg-mysql'
}
```

如果需要配置，则进入  config/default.js 进行配置