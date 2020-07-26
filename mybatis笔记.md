# Mybatis概述
持久层框架
封装JDBC,只关注Sql本身,不关注驱动注册、创建连接等重复过程。
ORM思想 
orm：Object Relational Mapping 对象关系映射
对象关系映射：数据库表与实体类对应，表字段与实体的属性对应，操作实体类就是操作数据库表。

# MyBatis环境搭建

## 创建maven项目添加依赖坐标
  IDE使用idea,选中New Project -> Maven -> 添加GroupId和ArtifactId,然后一路next。

修改pom.xml导入坐标
> step1:设置打包方式
```xml
<packaging>jar</packaging>
```
> step2:将官网的mybatis坐标加入pom.xml
```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version></dependency>
```
选中x.x.x改成3,在idea中会提示当前可用的mybatis版本,这里是3.5.5

>step3: 添加mysql驱动坐标
```xml

```
>step4:可选的,添加log4j,junit

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version></dependency>
<dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13</version>
        <scope>test</scope>
</dependency>
```
## 创建实体类

包括创建数据库表、实体类和dao接口

### 创建User表

```sql
CREATE TABLE `user` (
    `id` INT (20) NOT NULL AUTO_INCREMENT,
    `username` VARCHAR(50) NOT NULL COMMENT '用户姓名',
    `birthday` DATETIME DEFAULT NULL COMMENT '生日',
    `sex` CHAR(1) DEFAULT NULL COMMENT '性别',
    `address` VARCHAR (256),
    PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8;
INSERT INTO `user` (`username`, `birthday`, `sex`, `address`) VALUES
('张三','2018-01-01 15:52:41','1','北京'),
('李四','2018-01-01 15:52:41','0','南京'),
('郭靖','2018-01-01 15:52:41','1','牛家村'),
('黄蓉','2018-01-01 15:52:41','0','桃花岛'),
('杨过','2018-01-01 15:52:41','1','湖南'),
('小龙','2018-01-01 15:52:41','0','终南山'),
('郭襄','2018-01-01 15:52:41','0','桃花岛'),
('郭芙','2018-01-01 15:52:41','0', '桃花岛'),
('牛牛','2018-01-01 15:52:41','1','深圳');
```

### 创建实体类User

在src下建立User.java类:com.study.domain.User

```java
package com.study.domain;
import java.util.Date;
public class User {
    private long id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;

    public long getId() {
        return id;
    }
    public void setId(long id) {
        this.id = id;
    }
    public String getUsername() {
        return username;
    }
    public void setUsername(String username) {
        this.username = username;
    }
    public Date getBirthday() {
        return birthday;
    }
    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }
    public String getSex() {
        return sex;
    }
    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}

```
### 创建接口IUserDao

创建持久层接口IUserDao
```java
package com.study.dao;
import com.study.domain.User;
import java.util.List;
/**
 * User的持久层接口
 */
public interface IUserDao {
    /**
     * 查询所有数据
     * @return
     */
    public List<User> findAll();
}
```

## 创建配置文件

- MySql应用的核心是一个SQLSessionFactory实例.
- 通过xml配置文件构建SQLSessionFactory 

为了区分,这个核心的Xml配置文件称呼它为"MyBatis主配置文件"，它包含mybatis最核心的配置。

### MyBatis主配置文件(一般起名SqlMapConfig.xml)

一个最简单的主配置文件包含2部分,一个是mybatis环境依赖，包括数据源、事务管理器；另一个是mybatis映射器。

- 环境配置 `<environment>`标签
- 映射器配置 `<mapper>` 标签

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

它包含三部分内容:
- XML 头部的声明，它用来验证 XML 文档的正确性。
- environment 元素体中包含了事务管理和连接池的配置。
  - transactionManager 配置事务的类型
  - dataSource 配置数据源(也叫连接池),这里使用POOLED连接池
- mappers 元素则包含了一组映射器（mapper），这些映射器的 XML 映射文件包含了 SQL 代码和映射定义信息。
  

`SqlMapConfig.xml` 最终配置如下:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://127.0.0.1:3306/springdb?serverTimezone=UTC&amp;characterEncoding=utf8&amp;useUnicode=true&amp;useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="baoqy"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/study/dao/IUserDao.xml"/>
    </mappers>
</configuration>
```

这里数据库是mysql8。
在src/main/resources下按指定的目录结构新建文件`IUserDao.xml`:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.study.dao.IUserDao">
   <select id="findAll" resultType="com.study.domain.User">
       select * from user
   </select>
</mapper>
```
resultType 表示返回数据的结果应该对应的实体的类型

配置文件和映射文件的约束对比:

![20200726164822](https://raw.githubusercontent.com/gongfuxiaoying/picbed/master/20200726164822.png)


搭建步骤小结:
1. 创建maven工程并导入坐标
2. 创建实体类和实体类Dao接口
3. 创建Mybatis的主配置文件:SqlMapConfig.xml
4. 创建mapper映射器 IUserDao.xml

注意事项:
1. IUserDao.xml 一般名字为XxxMapper.xml,这里为了理解与IUserDao.java名称保持一致。
2. 注意mapper文件的所在目录结构与dao文件的目录结构一样，比如都是三级目录com/study/dao
   1. idea建立目录时有一个属性开关,点击齿轮形状的按钮【Show Option Menu -> Compact Middle Packages】,
      1. 勾选时，新建目录输入com/study/dao建立的时一级目录
      2. 不勾选，新建目录输入com/study/dao建立的时三级级目录

      ![20200726170252](https://raw.githubusercontent.com/gongfuxiaoying/picbed/master/20200726170252.png)
3. 映射器配置文件中的`namespace`属性取值必须是dao的全限定类名
4. 每个操作(`<select>`,`<insert>`等)的id对应dao接口的方法名

Mybatis这样的格式使我们不需要再写dao的实现类.

## 测试运行环境

使用junit4测试,在src/test下新建测试类IUserDaoTest

```Java
package com.study.dao;
import com.study.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

import static org.junit.Assert.*;


public class IUserDaoTest {
    @Test
    public void findAllTest() throws IOException {
        //https://mybatis.org/mybatis-3/zh/getting-started.html
        //1. 读取mybatis配置文件
        String resource = "SqlMapConfig.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        //2. 创建SQLSessionFactory工厂
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //3. 使用工厂生产SQLSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //4. 使用SQLSession创建dao接口的代理对象
        IUserDao userDao = sqlSession.getMapper(IUserDao.class);
        //5. 使用代理对象执行方法
        List<User> userList = userDao.findAll();
        for (User u: userList) {
            System.out.println(u);
        }
        //6. 关闭资源
        sqlSession.close();
        inputStream.close();
    }
}
```
总结一下测试步骤:
1. 读取mybatis配置文件
2. 创建SQLSessionFactory工厂
3. 使用工厂生产SQLSession对象
4. 使用SQLSession创建dao接口的代理对象
5. 使用代理对象执行方法
6. 关闭资源

## 入门示例使用的设计模式

1. 构建者模式
2. 工厂模式
3. 代理模式

### 构建者模式

```java
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
SqlSessionFactory sqlSessionFactory = builder.build(inputStream);
```
构建者模式可以屏蔽实现细节,这里隐藏了对象创建的细节,直接调用方法即可得到对象.
这里的`builder`就是创建者模式。

### 工厂模式

```java
SqlSession sqlSession = sqlSessionFactory.openSession();
```
生成sqlSession使用了工厂模式,工厂模式的特点是`解耦合`,降低类之间的依赖关系.

对于不同对象的生成在工厂里完成,我们使用只需要从工厂获取即可,不需要关心对象的生成.

### 代理模式

```Java
sqlSession.getMapper(IUserDao.class);
```
可以看到,没有IUserDao的实现做修改

具体代理分析参考:https://www.cnblogs.com/hopeofthevillage/p/11384848.html



