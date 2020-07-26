# Mybatis概述
持久层框架
封装JDBC,只关注Sql本身,不关注驱动注册、创建连接等重复过程。
ORM思想 
orm：Object Relational Mapping 对象关系映射
对象关系映射：数据库表与实体类对应，表字段与实体的属性对应，操作实体类就是操作数据库表。

# 环境搭建
## 新建maven项目
  IDE使用idea,选中New Project -> Maven -> 添加GroupId和ArtifactId,然后一路next
## 修改pom.xml导入坐标
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

## 创建数据库

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

## 