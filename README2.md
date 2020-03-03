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



## 后台处理
角色删除，使用虚拟删除。

