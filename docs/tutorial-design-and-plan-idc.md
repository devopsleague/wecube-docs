# 上手指引：设计并规划您的数据中心

现在，我们要使用刚刚安装好的WeCube平台在腾讯云上搭建一个完整的金融级数据中心。

## 确认数据中心的设计方案和配置信息

首先，通过WeCube菜单项 “**设计**” - “**机房资源规划**” 进入设计视图，选择 “`广州生产机房`” 、 “`广州生产3机房`” 、 “`广州生产4机房`” 并进行 “**查询**” ，您将会看到预置在WeCube中的整套数据中心设计方案。
在这个设计视图中，您可以通过切换页面标签来进一步查看数据中心规划中的 “网段” 、 “IP地址” 、 “网络区域” 、 “业务区域” 、 “资源集合” 、 “资源实例” 等配置项和资源的具体信息。

> [QUOTE]关于该设计方案的具体信息，请参见文档 “示例数据中心设计方案解析” 。？？？

### 执行专用的标准化流程任务编排来创建资源

现在，我们将要以执行任务编排的方式，为规划好的云上数据中心创建网络资源、计算资源和存储资源。

!!! note ""
    关于编排的设计与执行，请参考文档 “[用户手册：编排配置](manual-orchestration-configuration.md)” 和 “[用户手册：编排执行](manual-orchestration-execution.md)”。

#### 初始化网络区域中的网络资源

首先，请通过WeCube菜单项 “**执行**” - “**编排执行**” 进入编排执行页面，在这里，我们选择名为 “`L2_地域数据中心VPC级网络初始化`” 的任务编排来进行云上数据中心网络资源的创建与配置。这个任务编排已经将以下运维操作进行了标准化：

1. 为每个规划的网络区域创建独立的私有网络VPC，确保网络区域隔离性。
1. 在网络区域间创建对等连接和NAT网关，配置相应路由规则，确保网络通路畅通。
1. 为网络区域创建安全组和默认安全规则，确保数据中心初始化后的网络安全。
1. 在WeCube所在网络区域和数据中心间建立网络通路，满足应用部署、监控和其它运维操作的需要。

接着，在 “目标对象” 列表中，需要指定任务编排执行时涉及的范围，请选择 “`GZP`” （对应腾讯云华南地区）作为将要进行网络资源创建和初始化的数据中心。

> [QUOTE]关于任务编排 “地域数据中心VPC级网络初始化” 的设计和执行的详细信息，请参见文档 “标准化流程任务编排设计与执行” 的相应章节。？？？

执行完毕后，您可以在腾讯云控制台中找到新创建的私有网络、路由表、安全组等云资源，也可以在WeCube的CI数据中找到对应的已经创建好的云资源Id。这样，我们就完成了数据中心中网络区域的初始化。

#### 初始化业务区域和资源集合中的网络资源

在网络区域初始化完毕后，我们继续根据数据中心规划中的业务区域和资源集合对网络资源进一步细分并创建子网，从而形成可以容纳计算和存储资源的资源池，即资源集合。请在编排执行页面中选择名为 “`L2_业务区域子网网络初始化`” 的任务编排，它将完成以下标准化的运维操作：

1. 为资源集合创建独立子网，确保资源池的隔离及易于管理。
1. 为特殊的资源集合配置独立路由规则，以满足特殊的网络安全需要。

接着，在 “**目标对象**” 列表中，请选择 “`GZP2_SF_CS`” 作为将要进行网络资源创建和初始化的业务区域，它对应于我们在广州4可用区数据中心（`GZP4`）中核心业务服务网络区域（`SF`）中的通用服务业务区域（`CS`）。

> [QUOTE]关于任务编排 “业务区域子网网络初始化” 的设计和执行的详细信息，请参见文档 “标准化流程任务编排设计与执行” 的相应章节。？？？

执行完毕后，您可以在腾讯云控制台中找到新创建的子网、路由表等云资源，也可以在WeCube的CI数据中找到对应的已经创建好的云资源Id。这样，我们就完成了数据中心中业务区域和资源集合的网络资源初始化。

#### 完善WeCube平台所需的网络资源配置

为了使WeCube能够从数据中心的管理网络区域中正常访问其它网络区域中的资源，请在腾讯云控制台中找到名为 “`instance_wecube_platform`” 的运行WeCube的云服务器实例，将其关联到之前创建的名为 “`GZP2_MGMT`” 的安全组，这样就可以确保WeCube接下来进行应用部署和其它应为操作所需要的网络连接畅通。


#### 初始化业务区域和资源集合中的计算存储资源

在网络资源全部配置完毕后，我们就可以为资源集合创建云服务器、云数据库、云负载均衡器等计算和存储资源，以便分配给各业务应用系统使用。为此，请在编排执行页面中选择名为 “`L3_业务区域资源初始化`” 的任务编排，它将完成以下标准化的运维操作：

1. 为应用服务类的资源集合创建云服务器实例，满足资源集合中资源数量的设计需要。
1. 为云服务器实例配置默认安全组和安全规则，确保网络安全。
1. 为云服务器实例安装监控代理，并启动资源监控。
1. 根据需要对创建的云服务器实例执行初始化操作。
1. 为数据库类资源集合创建数据库运行实例。
1. 为数据库运行实例配置并启用监控。
1. 为负载均衡器类型的资源集合创建负载均衡器实例。
1. 为业务区域配置一个包含所有资源集合的统一监控视图。

接着，在 “**目标对象**” 列表中，请选择 “`GZP4_SF_CS`” 作为将要进行计算存储资源创建的业务区域，它对应于我们在广州4可用区数据中心（`GZP4`）中核心业务服务网络区域 （`SF`） 中的通用服务业务区域（`CS`）。

> [QUOTE]关于任务编排 “业务区域资源初始化” 的设计和执行的详细信息，请参见文档 “标准化流程任务编排的设计与执行” 的相应章节。？？？

执行完毕后，您可以在腾讯云控制台中找到新创建的云服务器、云数据库、云负载均衡器等资源实例，也可以在WeCube的CI数据中找到对应的已经创建好的云资源Id。至此，我们就完成了数据中心中业务区域和资源集合的所有资源初始化。

### 检查资源监控信息

在完成了资源集合中计算资源的创建之后，WeCube会为资源实例（云服务器实例、数据库实例等）注册并启动资源监控，您可以通过WeCube菜单项 “**监测**” - “**对象视图**” 进入相应页面查看。

关于监控的具体信息，请参见文档 “Open-Monitor监控插件”。

### 资源的释放和删除

您可以通过执行编排 “`L3_业务区域资源实例删除`” 和 “`L2_地域数据中心删除`” 将之前已经创建的云资源进行释放和删除。


## 使用WeCube在腾讯云上部署业务应用系统

现在，我们要把用于演示的业务应用系统部署到之前已经准备好的数据中心资源上。

### 确认业务应用系统的设计

首先，请通过WeCube菜单项 “**设计**” - “**应用架构设计**” 进入设计视图，选择 “`演示系统`” 并进行 “**查询**” 就可以看到与当前选定业务应用系统相关的的架构设计信息。

在默认显示的 “**应用逻辑图**” 页面标签中，我们可以看到，用于演示的业务应用系统包含一个业务应用部署单元APP和一个数据库部署单元DB，在实际部署到数据中心时，还会增加一个应用负载均衡器部署单元ALB，而客户端浏览器单元在这里则是为了完整展示单元间的调用关系。
切换至 “**物理部署图**” 页面标签后，页面上就会显示出当前业务应用系统在部署时各部署单元与数据中心中目标资源集合的对应关系，也就是各系统逻辑组件在物理环境中的目标部署位置。

### 管理业务应用系统的部署物料包

在了解业务应用系统的架构设计之后，我们需要为应用服务部署单元和数据库部署单元准备好用于部署的物料包，请通过以下途径获取用于演示的应用部署物料包：
？？？

接着，请通过WeCube菜单项 “**执行**” - “**应用物料管理**” 进入相关页面，在 “**系统设计版本**” 中选择 “`演示系统`”，然后在 “**系统设计列表中**” 选择一个部署单元并为它上传对应的部署物料包。在这里，我们需要为 “`演示业务应用`” 和 “`演示业务数据库`” 这两个部署单元分别上传它们各自的部署物料包并完成物料包的参数配置。

> [QUOTE]关于应用物料管理的具体信息，请参见文档 “应用物料管理使用指引”。？？？

!!! note ""
    关于应用部署物料包和发布过程的规范，请参见文档 “[应用发布规范](application-deployment-specification.md)”。

### 业务应用系统的部署

在执行部署之前，请通过WeCube菜单项 “**设计**” - “**应用部署设计**” 进入部署设计视图，并切换至 “**业务应用实例**” 页面标签，编辑其中的数据记录，更改它们的 “**部署包**” 属性，选择一个部署计划使用的应用物料包。保存已经编辑的数据后，可以看到它们的 “**部署包地址**” 和 “**差异配置值**” 属性值也已经相应地被更新，并将被用于下一次的应用部署。

接着，为了进行首次部署，请通过WeCube菜单项 “**执行**” - “**编排执行**” 进入执行页面，根据需要可以选择名为 “`L3_子系统首次部署`” 的任务编排，它将完成以下标准化的运维操作：

1. 创建部署用户
1. 部署数据库单元
1. 部署应用单元
1. 配置应用负载均衡单元
1. 注册并启用应用监控
1. 为子系统配置一个包含所有部署单元的统一监控视图

在 “**目标对象**” 列表中，请选择 “`PRD_DEMO_CORE`” 作为将要进行部署的子系统，它对应于我们在生产环境（`PRD`）中演示业务系统（`DEMO`）的核心子系统（`CORE`）。

> [QUOTE]关于任务编排 “子系统首次部署” 的设计和执行的详细信息，请参见文档 “标准化流程任务编排的设计与执行” 的相应章节。？？？

执行完毕后，数据库单元的部署脚本将在对应的数据库实例上执行；应用单元的部署包将被分发到指定的资源实例上的部署目录，并完成部署脚本的执行；同时，应用负载均衡单元上将增加对应的应用单元节点。这时，我们可以进一步确认业务应用的服务状态。

对于业务应用系统的升级部署，请在编排执行页面中执行名为 “`L3_子系统升级部署`” 的任务编排，并选择 “`PRD_DEMO_CORE`” 作为执行部署的业务应用子系统。

> [QUOTE]关于任务编排 “子系统升级部署” 的设计和执行的详细信息，请参见文档 “标准化流程任务编排的设计与执行” 的相应章节。？？？

### 检查应用监控信息

在完成了业务应用系统的部署之后，WeCube会为业务应用服务注册并启动应用监控，您可以通过WeCube菜单项 “**监测**” - “**对象视图**” 进入相应页面，输入系统、子系统或部署单元的名称后可以查询并查看以业务应用为视角进行组织的监控对象视图。

> [QUOTE]关于监控的具体信息，请参见文档 “Open-Monitor监控插件”。？？？


## 下一步

您可以继续阅读文档中心中用户手册下的其它文档内容：

- [安装WeCube](standalone-mode-on-public-cloud.md)
- [注册和配置插件](manual-plugin.md)
- [编排配置](manual-orchestration-configuration.md)
- [编排执行](manual-orchestration-execution.md)
- [批量执行](manual-batch-execution.md)


- 公有云软件系统一键交付 (Delivery By Terraform) 

- 示例数据中心设计方案解析？？？
- 标准化流程任务编排的设计与执行？？？
- 应用物料管理使用指引？？？

- CMDB使用手册？？？
- 数据模型链式表达式使用指引？？？

- 插件注册说明
- 编排配置说明文档
- 编排执行使用指引
- Open-Monitor监控插件说明文档
- 批量执行使用指引
- 应用发布规范文档