---
layout: post
title: Vue 笔记
category: 备忘
tags: Vue
keywords: Vue
description: Vue学习笔记
---
## 介绍
Vue.js是数据驱动的，DOM将和数据保持同步

#### MVVM模式
Model-View-ViewModel
双向绑定DOM Listeners和Data Bindings看作两个工具，它们是实现双向绑定的关键。

从View侧看，ViewModel中的DOM Listeners工具会帮我们监测页面上DOM元素的变化，如果有变化，则更改Model中的数据；
从Model侧看，当我们更新Model中的数据时，Data Bindings工具会帮我们更新页面中的DOM元素。

## Hello World

万事开头容易，看一下vue的hello world怎么写

    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="UTF-8">
            <title></title>
        </head>

        <body>
            <!--这是我们的View-->
            <div id="app">
                {{ message }}
            </div>
        </body>
        <script src="js/vue.js"></script>
        <script>
            // 这是我们的Model
            var exampleData = {
                message: 'Hello World!'
            }

            // 创建一个 Vue 实例或 "ViewModel"
            // 它连接 View 与 Model
            new Vue({
                el: '#app',
                data: exampleData
            })
        </script>
    </html>


#### 使用Vue的过程就是定义MVVM各个组成部分的过程的过程。

1. 定义View
2. 定义Model
3. 创建一个Vue实例或"ViewModel"，它用于连接View和Model


#### 双向绑定示例

MVVM模式本身是实现了双向绑定的，在Vue.js中可以使用v-model指令在表单元素上创建双向数据绑定。


    <!--这是我们的View-->
    <div id="app">
        <p>{{ message }}</p>
        <input type="text" v-model="message"/>
    </div>

将message绑定到文本框，当更改文本框的值时，<p>{{ message }}</p> 中的内容也会被更新。反过来，如果改变message的值，文本框的值也会被更新，我们可以在Chrome控制台进行尝试。

### Vue.js的常用指令

#### v-if指令

v-if是条件渲染指令，它根据表达式的真假来删除和插入元素

    <div id="app">
                <h1>Hello, Vue.js!</h1>
                <h1 v-if="yes">Yes!</h1>
                <h1 v-if="no">No!</h1>
                <h1 v-if="age >= 25">Age: {{ age }}</h1>
                <h1 v-if="name.indexOf('jack') >= 0">Name: {{ name }}</h1>
            </div>
            
#### v-show指令
v-show也是条件渲染指令，和v-if指令不同的是，使用v-show指令的元素始终会被渲染到HTML，它只是简单地为元素设置CSS的style属性。
```
		<div id="app">
			<h1>Hello, Vue.js!</h1>
			<h1 v-show="yes">Yes!</h1>
			<h1 v-show="no">No!</h1>
			<h1 v-show="age >= 25">Age: {{ age }}</h1>
			<h1 v-show="name.indexOf('jack') >= 0">Name: {{ name }}</h1>
		</div>
```

#### v-else指令
可以用v-else指令为v-if或v-show添加一个“else块”。
```
		<div id="app">
			<h1 v-if="age >= 25">Age: {{ age }}</h1>
			<h1 v-else>Name: {{ name }}</h1>
			<h1>---------------------分割线---------------------</h1>
			<h1 v-show="name.indexOf('keep') >= 0">Name: {{ name }}</h1>
			<h1 v-else>Sex: {{ sex }}</h1>
		</div>
```
        
#### v-for指令

```
        <div id="app">
				<tbody>
					<tr v-for="person in people">
						<td>{{ person.name  }}</td>
						<td>{{ person.age  }}</td>
						<td>{{ person.sex  }}</td>
					</tr>
				</tbody>
        </div>
                
    <script>
		var vm = new Vue({
			el: '#app',
			data: {
				people: [{
					name: 'Jack',
					age: 30,
					sex: 'Male'
				}, {
					name: 'Bill',
					age: 26,
					sex: 'Male'
				}, {
					name: 'Tracy',
					age: 22,
					sex: 'Female'
				}, {
					name: 'Chris',
					age: 36,
					sex: 'Male'
				}]
			}
		})
	</script>
```

#### v-bind指令
v-bind指令可以在其名称后面带一个参数，中间放一个冒号隔开，这个参数通常是HTML元素的特性（attribute），例如：v-bind:class

```
		<div id="app">
			<ul class="pagination">
				<li v-for="n in pageCount">
					<a href="javascripit:void(0)" v-bind:class="activeNumber === n + 1 ? 'active' : ''">{\{ n + 1 }\}</a>
				</li>
			</ul>
		</div>
        
    <script>
		var vm = new Vue({
			el: '#app',
			data: {
				activeNumber: 1,
				pageCount: 10
			}
		})
	</script>
```

#### v-on指令

v-on指令用于给监听DOM事件，它的用语法和v-bind是类似的，例如监听<a>元素的点击事件：

```
    <a v-on:click="doSomething">
```

有两种形式调用方法：绑定一个方法（让事件指向方法的引用），或者使用内联语句。
```		
        <div id="app">
			<p><input type="text" v-model="message"></p>
			<p>
				<!--click事件直接绑定一个方法-->
				<button v-on:click="greet">Greet</button>
			</p>
			<p>
				<!--click事件使用内联语句-->
				<button v-on:click="say('Hi')">Hi</button>
			</p>
		</div>


	<script>
		var vm = new Vue({
			el: '#app',
			data: {
				message: 'Hello, Vue.js!'
			},
			// 在 `methods` 对象中定义方法
			methods: {
				greet: function() {
					// // 方法内 `this` 指向 vm
					alert(this.message)
				},
				say: function(msg) {
					alert(msg)
				}
			}
		})
	</script>
```

#### v-bind和v-on的缩写
Vue.js为最常用的两个指令v-bind和v-on提供了缩写方式。v-bind指令可以缩写为一个冒号，v-on指令可以缩写为@符号。
```
    <!--完整语法-->
    <a href="javascripit:void(0)" v-bind:class="activeNumber === n + 1 ? 'active' : ''">{\{ n + 1 }\}</a>
    <!--缩写语法-->
    <a href="javascripit:void(0)" :class="activeNumber=== n + 1 ? 'active' : ''">{\{ n + 1 }\}</a>

    <!--完整语法-->
    <button v-on:click="greet">Greet</button>
    <!--缩写语法-->
    <button @click="greet">Greet</button>
```
## 组件系统

组件系统是Vue.js其中一个重要的概念，它提供了一种抽象，让我们可以使用独立可复用的小组件来构建大型应用，任意类型的应用界面都可以抽象为一个组件树

#### 基本步骤
Vue.js的组件的使用有3个步骤：创建组件构造器、注册组件和使用组件。

1. Vue.extend()
2. Vue.component()
3. 在实例作用范围内使用组件
```
		<div id="app">
			<!-- 3. #app是Vue实例挂载的元素，应该在挂载元素范围内使用组件-->
			<my-component></my-component>
		</div>

	    <script>
	
		// 1.创建一个组件构造器
		var myComponent = Vue.extend({
			template: '<div>This is my first component!</div>'
		})
		
		// 2.注册组件，并指定组件的标签，组件的HTML标签为<my-component>
		Vue.component('my-component', myComponent)
		
		new Vue({
			el: '#app'
		});
		
	    </script>
```
使用组件和使用普通的HTML元素没什么区别

#### 理解组件的创建和注册

1. Vue.extend()是Vue构造器的扩展，调用Vue.extend()创建的是一个组件构造器。 
2. Vue.extend()构造器有一个选项对象，选项对象的template属性用于定义组件要渲染的HTML。 
3. 使用Vue.component()注册组件时，需要提供2个参数，第1个参数时组件的标签，第2个参数是组件构造器。 
4. 组件应该挂载到某个Vue实例下，否则它不会生效。

#### 全局注册和局部注册
如果不需要全局注册，或者是让组件使用在其它组件内，可以用选项对象的components属性实现局部注册。
```
	<body>
		<div id="app">
			<!-- 3. my-component只能在#app下使用-->
			<my-component></my-component>
		</div>
	</body>
	<script src="js/vue.js"></script>
	<script>
		// 1.创建一个组件构造器
		var myComponent = Vue.extend({
			template: '<div>This is my first component!</div>'
		})
		
		new Vue({
			el: '#app',
			components: {
				// 2. 将myComponent组件注册到Vue实例下
				'my-component' : myComponent
			}
		});
	</script>
```

#### 父组件和子组件

我们可以在组件中定义并使用其他组件，这就构成了父子组件的关系。
```
	<body>
		<div id="app">
			<parent-component>
			</parent-component>
		</div>
	</body>
	<script src="js/vue.js"></script>
	<script>
		
		var Child = Vue.extend({
			template: '<p>This is a child component!</p>'
		})
		
		var Parent = Vue.extend({
			// 在Parent组件内使用<child-component>标签
			template :'<p>This is a Parent component</p><child-component></child-component>',
			components: {
				// 局部注册Child组件，该组件只能在Parent组件内使用
				'child-component': Child
			}
		})
		
		// 全局注册Parent组件
		Vue.component('parent-component', Parent)
		
		new Vue({
			el: '#app'
		})
		
	</script>
```

#### 组件注册语法糖

使用Vue.component()直接创建和注册组件
```
    // 全局注册，my-component1是标签名称
    Vue.component('my-component1',{
        template: '<div>This is the first component!</div>'
    })

    var vm1 = new Vue({
        el: '#app1'
    })
```

Vue.component()的第1个参数是标签名称，第2个参数是一个选项对象，使用选项对象的template属性定义组件模板。
使用这种方式，Vue在背后会自动地调用Vue.extend()。
```
    var vm2 = new Vue({
        el: '#app2',
        components: {
            // 局部注册，my-component2是标签名称
            'my-component2': {
                template: '<div>This is the second component!</div>'
            },
            // 局部注册，my-component3是标签名称
            'my-component3': {
                template: '<div>This is the third component!</div>'
            }
        }
    })
```

#### 使用script或template标签

尽管语法糖简化了组件注册，但在template选项中拼接HTML元素比较麻烦，这也导致了HTML和JavaScript的高耦合性。
```
		<script type="text/x-template" id="myComponent">
			<div>This is a component!</div>
		</script>
        
       <template id="myComponent">
			<div>This is a component!</div>
		</template>
```

#### 组件的el和data选项
Vue.js规定：在定义组件的选项时，data和el选项必须使用函数。
```
    Vue.component('my-component', {
        data: function(){
            return {a : 1}
        }
    })
```

#### 使用props

组件实例的作用域是孤立的。不应该在子组件的模板内直接引用父组件的数据。可以使用 props 把数据传给子组件。
```
    var vm = new Vue({
        el: '#app',
        data: {
            name: 'keepfool',
            age: 28
        },
        components: {
            'my-component': {
                template: '#myComponent',
                props: ['myName', 'myAge']
            }
        }
    })
```

#### prop的绑定类型

#### 单向绑定
prop默认是单向绑定：当父组件的属性变化时，将传导给子组件，但是反过来不会。

#### 双向绑定
可以使用.sync显式地指定双向绑定，这使得子组件的数据修改会回传给父组件。
```
    <my-component v-bind:my-name.sync="name" v-bind:my-age.sync="age"></my-component>
```

#### 单次绑定
```
    <my-component v-bind:my-name.once="name" v-bind:my-age.once="age"></my-component>
```

#### 父子组件之间的访问

    父组件访问子组件：使用$children或$refs
    子组件访问父组件：使用$parent
    子组件访问根组件：使用$root

#### 自定义事件

    使用 $on() 监听事件；
    使用 $emit() 在它上面触发事件；
    使用 $dispatch() 派发事件，事件沿着父链冒泡；
    使用 $broadcast() 广播事件，事件向下传导给所有的后代。

#### 组件的API

    prop 允许外部环境传递数据给组件；
    事件 允许组件触发外部环境的 action；
    slot 允许外部环境插入内容到组件的视图结构内。

### 跨域资源共享
跨域资源共享（CORS）是一份浏览器技术的规范，提供了Web服务器从不同网域传来沙盒脚本的方法，以避开浏览器的同源策略，是JSONP模式的现代版。

#### JSONP概念

由于同源策略，一般来说不允许JavaScript跨域访问其他服务器的页面对象，但是HTML的script元素是一个例外。利用script元素的这个开放策略，网页可以得到从其他来源动态产生的 JSON资料，而这种使用模式就是所谓的 JSONP。用 JSONP 抓到的资料并不是 JSON，而是任意的JavaScript，用 JavaScript 直译器执行而不是用 JSON 解析器解析。

#### 跨域解决方案

在服务端启用CORS。
让无服务端拥有处理JSONP的能力。

这两种跨域解决方案的区别是什么呢？
JSONP只支持GET请求；CORS则支持GET、POST、PUT、DELETE等标准的HTTP方法
JSONP从服务端获取到的是script文件；CORS则是一段XML或JSON或其他格式的数据

#### vue-resource
vue-resource是Vue.js的一款插件，它可以通过XMLHttpRequest或JSONP发起请求并处理响应。也就是说，$.ajax能做的事情，vue-resource插件一样也能做到，而且vue-resource的API更为简洁。

引入vue-resource
```
    <script src="js/vue.js"></script>
    <script src="js/vue-resource.js"></script>
```

基本语法
```
    // 传统写法
    this.$http.get('/someUrl', [options]).then(function(response){
        // 响应成功回调
    }, function(response){
        // 响应错误回调
    });


    // Lambda写法
    this.$http.get('/someUrl', [options]).then((response) => {
        // 响应成功回调
    }, (response) => {
        // 响应错误回调
    });
```    

支持的HTTP方法    

    get(url, [options])
    head(url, [options])
    delete(url, [options])
    jsonp(url, [options])
    post(url, [body], [options])
    put(url, [body], [options])
    patch(url, [body], [options])
    
options对象

    参数	类型	描述
    url	string	请求的URL
    method	string	请求的HTTP方法，例如：'GET', 'POST'或其他HTTP方法
    body	Object, FormData string	request body
    params	Object	请求的URL参数对象
    headers	Object	request header
    timeout	number	单位为毫秒的请求超时时间 (0 表示无超时时间)
    before	function(request)	请求发送前的处理函数，类似于jQuery的beforeSend函数
    progress	function(event)	ProgressEvent回调处理函数
    credentials	boolean	表示跨域请求时是否需要使用凭证
    emulateHTTP	boolean	发送PUT, PATCH, DELETE请求时以HTTP POST的方式发送，并设置请求头的X-HTTP-Method-Override
    emulateJSON	boolean	将request body以application/x-www-form-urlencoded content type发送
    
#### 使用inteceptor

拦截器可以在请求发送前和发送请求后做一些处理。
```
    Vue.http.interceptors.push((request, next) => {
            // ...
            // 请求发送前的处理逻辑
            // ...
        next((response) => {
            // ...
            // 请求发送后的处理逻辑
            // ...
            // 根据请求的状态，response参数会返回给successCallback或errorCallback
            return response
        })
    })
```

#### OAuth认证流程

1. 用户通过浏览器打开客户端后，客户端要求用户给予授权。
2. 客户端可以直接将授权请求发给用户），或者发送给一个中间媒介，比如认证服务器。
3. 用户同意给予客户端授权，客户端收到用户的授权。
4. 授权模式(Grant Type)取决于客户端使用的模式，以及认证服务器所支持的模式。
5. 客户端提供身份信息，然后向认证服务器发送请求，申请访问令牌
6. 认证服务器验证客户端提供的身份信息，如果验证通过，则向客户端发放令牌
7. 客户端使用访问令牌，向资源服务器请求受保护的资源
8. 资源服务器验证访问令牌，如果有效，则向客户端开放资源

#### OAuth 2.0定义了四种授权方式

授权码模式（authorization code）
简化模式（implicit）
密码模式（resource owner password credentials）
客户端模式（client credentials）

### Webpack
当下最热门的前端资源模块化管理和打包工具，它可以将很多松散的模块按照依赖和规则打包成符合生产环境部署的前端资源，还可以将按需加载的模块进行代码分割，等到实际需要的时候再异步加载。

#### 生成项目

    vue init webpack-simple my-webpack-simple-demo

#### 项目结构说明

    ├─.babelrc		// babel配置文件
    ├─.gitignore	
    ├─index.html		// 主页
    ├─package.json		// 项目配置文件
    ├─README.md  
    ├─webpack.config.js	// webpack配置文件
    ├─dist			// 发布目录
    │   ├─.gitkeep       
    ├─src			// 开发目录	
    │   ├─App.vue		// App.vue组件
    │   ├─main.js		// 预编译入口

#### 安装项目依赖

    cd my-webpack-simple-demo
    npm install

#### 运行示例

    npm run dev

#### 发布
    
    npm run build

### vue-router

创建组件
```
    <script src="js/vue.js"></script>
    <script src="js/vue-router.js"></script>

    var Home = Vue.extend({
        template: '<div><h1>Home</h1><p>{{msg}}</p></div>',
        data: function() {
            return {
                msg: 'Hello, vue router!'
            }
        }
    })

    var About = Vue.extend({
        template: '<div><h1>About</h1><p>This is the tutorial about vue-router.</p></div>'
    })
```
创建Router
```
    var router = new VueRouter()
```
映射路由
```
    router.map({
        '/home': { component: Home },
        '/about': { component: About }
    })
```
使用v-link指令
```
    <div class="list-group">
        <a class="list-group-item" v-link="{ path: '/home'}">Home</a>
        <a class="list-group-item" v-link="{ path: '/about'}">About</a>
    </div>
```
使用<router-view>标签
```
    <router-view></router-view>
```   
启动路由

```   
    var App = Vue.extend({})
    router.start(App, '#app')
```   
    
#### JavaScript

创建组件：创建单页面应用需要渲染的组件
创建路由：创建VueRouter实例
映射路由：调用VueRouter实例的map方法
启动路由：调用VueRouter实例的start方法

#### HTML

使用v-link指令
使用<router-view>标签

#### router.redirect

应用在首次运行时右侧是一片空白，应用通常都会有一个首页，例如：Home页。

```   
    router.redirect({
        '/': '/home'
    })
```   