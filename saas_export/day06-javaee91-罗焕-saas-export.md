## 今日内容

## shiro的概念内容

**shiro主要有三个概念**：

**Subject**：代指当前操作的用户（可以是某种系统）

**SecurityManager**：shiro核心，内部的安全祖先，一般不关心

**Realm：连接数据库，可以有多个，但至少有一个

**程序员关注点**：

1. Subject如何获取；
2. Realm如何定义；
3. 密码比较器如何定义。

**过滤器别名说明**：

anon：匿名（所有都访问）（只要是“人”就能访问，不做处理）

authc：登录检查的过滤器（只有登录成功可以访问）

perms：权限校验过滤器（具有某种权限才可以访问）

















**新发现问题**：

之前所写用户数据更新中，更新数据的密码项未进行加密保存；

**解决办法**：

在UserService中update方法中添加加密API

**出现问题**：

数据库密码保存不进去

日志：log4j日志中 显示update 数目为0

**原因**：

![1560937779613](F:\黑马程序员\MyAtomNotes\images\1560937779613.png)

在从save方法中复制加密API是将设置UUID语句一起复制进去，导致每次都会重新设置UUID，找不到原来数据了！