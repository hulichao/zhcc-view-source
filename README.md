# zhcc-view前端权限控制策略

### 写在前面
首先，之前的开发权限控制趋于后端化，以spring security 和 shiro 为代表，都是基于RBAC的思路，以资源为权限基础，再以角色来绑定资源，最后通过角色来绑定用户。这种方式达到权限与用户的单独解耦，另外如果想增加一层，比如不同部门的相同角色，也可以按此方式增加即可。
### 利用Vue + Vue Router来实现
相对轻量级的前端框架以及配套的路由方案，与后端的权限即可对应。

> 为什么不使用 LocalStorage / SessionStorage / cookies 去保存登录凭据？这样可以全局访问很方便啊

首先是安全问题，不过不是主要问题，在有的方案里面提到，及时更新也是一种不错的方案，所以一种方式就是响应式Session。然后把处理session的js引用到所有组件中，这样就可以在组件内部访问到所有的变量和方法，当然也可以在引用js中访问。所以采用`import { session$ } from '@/mixins/session'` 来达到全局变量的优势，而且能够享受到Vue原本就有的响应式、计算属性等特性。简直就是一个完美的闭包变量。
上面的方案有个问题就是没有用动态路由，导致用户登录前不能初始化Vue应用，所以登陆页只能单独做，开始我也是这么做的，但始终觉得url跳转的体验不好，所以用动态路由解决了 的解决方案：如果您的公司没有 SSO，那么每个项目都只能重复造轮子做登录页（之前我司就是如此），此时只能借助 vue-router 的钩子函数 beforeEach 控权：

其实有一个难点：就是在数据库实时改变权限，页面可以不做任何调整，自动响应（刷新都不需要）。
另外一个点：用到的实际上是vue + vuex + vue Router
最终参考:



### 参考
1. [用mixin全局共享状态实现的前端权限](https://github.com/OneWayTech/vue-auth-solution)
2. [基于Vue/Vue-Router/axios实现的前端用户权限控制解决方案](https://refined-x.com/2017/11/28/Vue2.0%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/)
3.
## Donate
If you are enjoying this app, please consider making a donation to keep it alive, I will try my best to dedicate more time or even full time to work on it.
