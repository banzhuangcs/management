# Management

&nbsp;&nbsp;&nbsp;&nbsp;该系统是后台管理系统的一个子模块，是以用户数据为基础主要展示对用户数据的`展示`、`排序`、`分页`、`搜索`、`新增`、`删除`、`修改`等基本功能。  
&nbsp;&nbsp;&nbsp;&nbsp;目的是为了让大家对系统的`颜色搭配`、`显示状态`、`交互方式`进行评估。

# Usage

1. 安装npm中国镜像  
> npm i -g cnpm --registry=https://registry.npm.taobao.com  

2. 开发环境
> cnpm start

3. 生成环境
> cnpm run build


# 技术选型

&nbsp;&nbsp;&nbsp;&nbsp;该系统由于现在是采用前端Mock一些假数据，前后端分离，采用后端输出接口，前端处理和显示，基于这种完全由数据驱动ui，又是以组件进行构建页面的方式，所以主体框架采用React + Redux的形式。  

&nbsp;&nbsp;&nbsp;&nbsp;因为后台管理系统是以功能性操作和展示为主的网站，因为组件是构成页面的唯一元素，那么对ui组件框架应该是基于React封装的，考虑选择的是Antd框架，它的优点在于`文档清晰`，**Container Component**和**Show Component**`职责分层清楚`，仅仅只从上一级Component的`props单向的接收数据`，避免过多的数据入口造成的代码不可维护性。

# 开发步骤

遵循着 Model -> Service -> Component这样流程进行开发

* 项目结构划分  
* 提供数据访问地址
* 抽离Model
* 编写Service
* 设计组件
* CSS Modules
* Layout

# 具体实现

** 项目结构划分 **  
> 进行项目结构划分，可以很清晰的体现项目的功能  
cnpm i -g vue-cli
vue new app(创建的项目名)


    ├── /mock/           # 数据mock的接口文件
    ├── /src/            # 项目源码目录
    │ ├── /components/   # 项目组件
    │ ├── /routes/       # 路由组件（页面维度）
    │ ├── /models/       # 数据模型
    │ ├── /services/     # 数据接口
    │ ├── /utils/        # 工具函数
    │ ├── route.js       # 路由配置
    │ ├── index.js       # 入口文件
    │ ├── index.less     
    │ └── index.html     
    ├── package.json     # 项目信息
    └── proxy.config.js  # 数据mock配置

** 提供数据访问地址 **
> 快速模仿真实后端访问地址，让前端在开发阶段中可以独立于后端开发，减少后续联调的沟通成本。  
cnpm start 启动开发环境后，dora会将我们在proxy.config.js书写的访问地址映射到我们开启的本地服务器中
使用mockjs快速的根据生成规则来产生符合规则的数据


    export default {
      'GET /api/users': () => Mock.mock({ 'data|100', [{ 'id|+1': 0, name: '@cname' }] })
    }

** 抽离Model **  

后台管理系统其实就是对数据的`查询`和`操作`，复杂点就是如何组织这些数据。  
* 查询(Select)
* 操作(Create、Delete、Update)  

&nbsp;&nbsp;&nbsp;&nbsp;因为我们基于`React`组件化开发的思想，在组件化思想中，组件是构建页面的唯一元素，组件的`显示状态`、`显示位置`、`Event Handler`、`成员属性`都是依赖于传给该组件的数据，也可以说是数据驱动UI。  
所以在组件化思想中，数据不是独立的，而是与组件的状态信息绑定在一起的。  
&nbsp;&nbsp;&nbsp;&nbsp;在这里采用的是按照`业务维度`来进行抽离Model，将数据和组件的状态信息统一抽象为一系列的方法。  


    export default {
      namespace: 'users',
      state: { list: [] },
      effects: { *query() {} },
      reduces: { showLoading() {} },
    }


** 编写services **  

&nbsp;&nbsp;&nbsp;&nbsp;独立出数据和操作其本身的方法


    export async function query () {}  
** 设计组件 **  

&nbsp;&nbsp;&nbsp;&nbsp;基于React组件化开发思想，每个组件都是相对独立的，每个组件专注于做自己的事这个原则可以将组件进行分类
* Container Component
* OnlyShow  Component  

> Container Component的职责是组织好OnlyShow Component所需要的数据，控制其的交互逻辑、显示状态和显示位置
> OnlyShow  Component只显示Container Component传过来的数据和自身的样式

`对组件进行分类，其实就是避免了数据多个操作入口，统一由组件各自的Model传输，避免零散、组件到处都在处理数据，带来的后期维护上的困难`  

**  CSS Modules **
&nbsp;&nbsp;&nbsp;&nbsp;利用webpack的loader的css?modules开启style的命名空间的书写方式  

** Layout **
