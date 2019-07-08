## 今日内容：

​	Mybatis逆向工程；购销合同增删改查；货物增删改查

#### Mybatis Generator:

​	**作用**：从数据库表逆向生成

​	**步骤**：

​		导入**依赖**，

​		**mybatis-generator-config.xml**配置文件： 

​			**关注点**：

​		1、指定数据库jdbc驱动的位置；

​		2、数据库连接的信息--驱动类，连接地址，用户名，密码；

​		3、指定包名生成实体类、以及生成的地址；

​		4、根据包名生成Mapper映射文件、以及生成地址；

​		5、根据包名生成Dao接口、以及生成地址；

​		6、指定数据库名称、表名称、实体类名称、接口和映射文件名称。

​		写**Java**代码。

**逆向生成的方法**：

1、**int deleteByPrimaryKey(String id)**:根据id删除；

2、**Factory selectByPrimaryKey(String id)**:根据id查询；

3、**int insert(Factory record)**:全字段保存；

4、**int insertSelective(Factory record)**:可选列保存；

5、**int updateByPrimaryKey(Factory record)**:全字段更新，**对于未更新字段会更新成null**；

6、**int updateByPrimaryKeySelective(Factory record)**:可选列更新，避免了上述情况。

7、**List<Factory> selectByExample(example)**:条件查询

​	步骤：

​	i、创建example对象，

​	FactoryExample example = new FactoryExample(); 

​	//FactoryExample类是逆向工具自动生成的，当时生成两个类--Factory  和  FactoryExample；

​	ii、根据example创建条件对象criteria：

​	FactoryExample.criteria criteria = example.createCriteria();

​	iii、在criteria中添加条件和查询方式

**criteria.andFactoryNameEqualTo("天顺").andContactsEqualTo("海");**

criteria打点添加查询条件，多个查询条件继续打点添加。查询条件中，equalTo未等于，like为模糊查询，等。

#### 购销合同管理；

#### 货物管理：

ContractProductServiceImpl.java中保存货物、更新货物的金额。

