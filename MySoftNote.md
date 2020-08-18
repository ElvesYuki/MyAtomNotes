## 1、文档对比软件 Beyond Compare 4

评估版本延期，删除操作：

删除安装目录下的BCUnrar.dll文件
C:\Program Files\Beyond Compare 4\BCUnrar.dll,这个文件重命名或者直接删除，则会新增30天试用期，再次打开提示还有28天试用期
一劳永逸，修改注册表
1)在搜索栏中输入 regedit ，打开注册表
2)删除项目：计算机\HKEY_CURRENT_USER\Software\ScooterSoftware\Beyond Compare 4\CacheId

## 2、通过navicat工具导出word编写数据库设计文档

打开数据库，执行如下查询语句

SELECT
COLUMN_NAME 代码,
COLUMN_COMMENT 名称,
COLUMN_TYPE 数据类型,
'false' as 是键,
COLUMN_COMMENT 注释
FROM
INFORMATION_SCHEMA.COLUMNS
where
-- wx 为数据库名称，到时候只需要修改成你要导出表结构的数据库即可
table_schema ='wx'
AND
-- article为表名，到时候换成你要导出的表的名称
-- 如果不写的话，默认会查询出所有表中的数据，这样可能就分不清到底哪些字段是哪张表中的了，所以还是建议写上要导出的名名称
table_name = 't_sys_info'
