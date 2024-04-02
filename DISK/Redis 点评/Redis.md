---

类型: 笔记
创建日期: 2024-03-29
修改日期: 2024-03-31
---

# 基础配置
使用wsl2，利用redis.io上的wsl2教程直接命令行安装
> sudo service redis-server start启动服务
> redis-cli 在命令行中打开

- [ ] 问题：我在windows中无法用wsl2在局域网中的地址访问redis，但是直接输入127.0.0.1就可以，这是怎么回事

## cache aside 更新
数据库更新耗时远大于删缓存，右边的情况相较左边更难出现。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1710510807749-bf2aff48-6bed-4860-9b0e-78d6ec5ed37c.png#averageHue=%23f4f2f2&clientId=u40ad86d1-770e-4&from=paste&height=431&id=u1569afc2&originHeight=711&originWidth=1395&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=100683&status=done&style=none&taskId=u1c2d0d75-47cf-476f-95ab-38af33a1cec&title=&width=845.4544965885916)
#### 缓存更新策略的最佳实践方案：
低一致性需求：使用Redis自带的内存淘汰机制
高一致性需求：主动更新，并以超时剔除作为兜底方案
读操作：缓存命中则直接返回，缓存未命中则查询数据库，并写入缓存，设定超时时间
写操作：先写数据库，然后再删除缓存要确保数据库与缓存操作的原子性



