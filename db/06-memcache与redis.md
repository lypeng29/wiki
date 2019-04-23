# db 06-memcache与redis

memcache key/val存储
redis k/v list set hash

memcache 存储在内存，断电后会挂掉，数据量不能超过内存大小
redis 有部分存储在硬盘，支持持久化，重启后可以再次加载。支持master-slave模式备份


k/v存储选memcache，其他redis


redis使用场景
1.会话缓存
购物车数据，session

2.消息队列
注册发送手机验证码，邮件

3.全页缓存
商品列表页面，同样的筛选条件避免再次查询

4.排行榜、统计数据
