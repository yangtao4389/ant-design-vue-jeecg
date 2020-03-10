# 适配仁和安信公司


### 改变首页
views/Analysis.vue  被调用 配置在菜单列表页


### 设置用户信息
注册问题
全局使用了
src\components\layouts\UserLayout.vue
全局调用
src\config\router.config.js  配置了path，并在这里包含了登录、注册组件
系统如何默认跳转到登录？  path: '/user',
src\router\README.md 这里详细描述了路由的具体走向

### 请求的信息
JeecgListMixin
使用了这里的方法，所以有很多自带属性在里面。

## 后台处理
角色删除，使用虚拟删除。

### 修改中 20200307
views\scyd\ScydDayReportsChildList.vue  参考 views\list\PermissionList.vue
后台也需要参考这样获取数据。

现在没解决的是  查询，重置，这些操作 是无效的
src\views\system\LogList.vue
这里有日期的格式显示


Flex 布局是基于 24 栅格来定义每一个『盒子』的宽度，但不拘泥于栅格。

src\views\scyd\ScydDayReportsVideoList.vue
再添加的功能有：
分屏展示，展示动态图
展示列表
导出功能使用前端自带的，而不是后端生成

yarn add v-charts echarts
替换原先的表

分页查询 还有问题
后端分页不做处理，前端分页


