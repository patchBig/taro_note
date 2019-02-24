百度小程序不支持 template 导致不能用 wxParse 组件
解决：自己编写组件解决

百度小程序中组件通信有问题（按钮点击关闭）偶现
猜测：编译的问题

setState 的操作多的话，会导致页面中数据渲染有问题
减少 setState 的操作

从原生编译成 taro 语法的时候，属性名会发生变化
<Swiper> 中 onChange 会变成 onHange
