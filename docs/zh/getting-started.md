# 快速开始

在开始之前，我们假定您已经：

- 熟悉 Java 开发和及其环境配置
- 熟悉 关系型 数据库，比如 MySql

## Hello World

**第 1 步：创建数据库表**

```sql
CREATE TABLE IF NOT EXISTS `tb_account`
(
    `id`        INTEGER PRIMARY KEY auto_increment,
    `user_name` VARCHAR(100),
    `age`       Integer,
    `birthday`  DATETIME
);
```

**第 2 步：创建 java 项目，并添加 Maven 依赖**

```xml
<dependency>
    <groupId>com.mybatis-flex</groupId>
    <artifactId>mybatis-flex-core</artifactId>
    <version>1.0.4</version>
</dependency>
```

**第 3 步：编写实体类**

> 这部分可以使用 Mybatis-Flex 的代码生成器来生成实体类哟，功能非常强大的。详情进入：[代码生成器章节](./codegen.md) 了解。

```java
@Table("tb_account")
public class Account {

    @Id(keyType = KeyType.Auto)
    private Long id;
    private String userName;
    private Integer age;
    private Date birthday;
    
    //getter setter
}
```
- 使用 `@Table("tb_account")` 设置实体类与表名的映射关系
- 使用 `@Id(keyType = KeyType.Auto)` 标识主键为自增

**第 4 步：通过 main 方法开始使用（无 Spring 的场景）**

```java
public class HelloWorld {
    public static void main(String... args) {

        //创建数据源
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setJdbcUrl("jdbc:mysql://127.0.0.1:3306/mybatis-flex");
        dataSource.setUsername("username");
        dataSource.setPassword("password");

        //配置数据源
        MybatisFlexBootstrap.getInstance()
                .setDatasource(dataSource)
                .addMapper(AccountMapper.class)
                .start();


        //示例1：查询 id=1 条数据
        Account account = MybatisFlexBootstrap.getInstance()
                .execute(AccountMapper.class, mapper ->
                        mapper.selectOneById(1)
                );
        
        //示例2：或者使用 Db + Row 查询
        String sql = "select * from tb_account where age > ?";
        List<Row> rows = Db.selectListBySql(sql, 18);
    }
}
```

> 以上的示例中， `AccountMapper.class` 为 Mybatis-Flex 通过 APT 自动生成，无需手动编码。
> 也可以关闭自动生成功能，手动编写 AccountMapper，更多查看 [APT 文档](./apt)。


## 更多示例

- 示例 1：[Mybatis-Flex 原生（非 Spring）](https://gitee.com/mybatis-flex/mybatis-flex/tree/main/mybatis-flex-test/mybatis-flex-native-test)
- 示例 2：[Mybatis-Flex with Spring](https://gitee.com/mybatis-flex/mybatis-flex/tree/main/mybatis-flex-test/mybatis-flex-spring-test)
- 示例 3：[Mybatis-Flex with Spring boot](https://gitee.com/mybatis-flex/mybatis-flex/tree/main/mybatis-flex-test/mybatis-flex-spring-boot-test)
- 示例 4：[Db + Row](https://gitee.com/mybatis-flex/mybatis-flex/blob/main/mybatis-flex-test/mybatis-flex-native-test/src/main/java/com/mybatisflex/test/DbTestStarter.java)