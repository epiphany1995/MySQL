# 1、初识mysql

## 1.1 什么是数据库

数据库 （DB，DataBase）

概念：数据仓库，软件，安装在操作系统之上（window，linux，mac...），SQL：可以存储大量的数据

作用：存储数据，管理数据

## 1.2 数据库分类

关系型数据库：（SQL）

- Mysql，Oracle，SqlServer，DB2
- 通过表与表之间，行和列之间的关系进行数据的存储，

非关系型数据库：（NoSQL：Not Only）

- Redis，MongDB
- 非关系型数据库，通过对象存储，通过对象的自身的属性来决定

**DBMS（数据管理系统）**

- 数据库的管理软件，科学有效的管理我们的数据。维护和获取数据
- Mysql：关系型数据库管理系统

# 2、Mysql安装

## 2.1 软件下载

mysql5.7 64位下载地址：

https://dev.mysql.com/downloads/mysql/5.7.html

## 2.2 操作步骤

1. 下载后得到zip压缩包

2. 解压到自己想要安装到的目录，例如：E:\Environment\mysql\mysql-5.7.29

3. 配置环境变量：E:\Environment\mysql\mysql-5.7.29\bin

4. 在mysql的安装目录下新建**my.ini文件**

   ```ini
   [mysql]
   # 设置mysql客户端默认字符集
   default-character-set=utf8 
   [mysqld]
   # 设置mysql的安装目录
   basedir=E:\Environment\mysql\mysql-5.7.29\
   # 设置mysql数据库的数据的存放目录
   datadir=E:\Environment\mysql\mysql-5.7.29\data
   # 设置3306端口
   port = 3306 
   # 首次跳过密码登录（修改密码之后注释）
   skip-grant-tables
   ```

5. 用管理员打开命令窗口,并切换到mysql下的bin目录，然后依次执行以下命令：

   ```text
   //安装服务
   mysqld --install
   //初始化数据文件
   mysqld --initialize-insecure --user=mysql
   //启动服务
   net start mysql
   //进入mysql管理界面
   mysql -u root -p 
   ```

6. 进入mysql管理界面之后，修改密码：

   ```text
   //修改密码
   update mysql.user set authentication_string=password('686995') where user='root' and Host='localhost';
   //刷新权限
   flush privileges;
   ```

7. 修改my.ini文件注释最后一句skip-grant-tables

8. 重启mysql即可正常使用

   ```
   net stop mysql
   net start mysql
   ```


# 3、使用SQLyog

1. **打开连接本地数据库**！![image-20200425151653562](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200425151653562.png)
2. **新建一个数据库!**![image-20200425152131147](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200425152131147.png)
3. **创建一张表**！![image-20200425152738755](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200425152738755.png)

# 4、数据库基本操作

操作数据库>操作数据库中的表>操作数据库中的表数据

## 4.1. 操作数据库（database）

1. 查看数据库

   ```sql
   SHOW DATABASES;
   ```

2. 创建数据库

   ```
   CREATE DATABASE [IF NOT EXISTS] mydatabase;
   ```

3. 删除数据库

   ```
   DROP DATABASE [IF EXISTS] mydatabase1;
   ```

## 4.2 数据库的数据类型

> 数值

- tinyint                   十分小的数据                          1个字节
- smallint                较小的数据                              2个字节     
- mediumint           中等大小的数据                      3个字节
- int                          标准的整数                              4个字节
- bigint                     较大的数据                             8个字节
- float                       浮点数                                      4个字节
- double                   浮点数                                     8个字节(有精度问题)
- decimal                 字符串形式的浮点数              金融计算的时候,一般使用decimal   

>字符串

- char                字符串固定大小的           0~255
- varchar           可变字符串                      0~65535  
- tinytext           微型文本                           2^8 -1
- text                   文本串                             2^16-1

> 时间日期

- date                  YYYY-MM-DD              日期格式
- time                  HH：mm：ss              时间格式
- datetime          YYYY-MM-DD HH：mm：ss         最常用的时间格式
- timestamp       时间戳                        1970.1.1到现在的毫秒数

## 4.3 数据库的字段属性

Unsigned:

- 无符号的整数
- 声明了改列不能为负数

Zeriofill

- 0填充的
- 不足的位数，用0来填充     int（3）  5 -> 005

自增：

- 通常理解为自增，自动在上一条记录的基础上加1 （默认）
- 通常用来设计唯一的主键 - index，必须是整数类型
- 可以自定义设计主键的自增起始值和步长

非空 

- 假设设置为not null，如果不给他赋值，就会报错
- null，如果不填写值，默认就是null

默认：

- 设置默认的值！
- 如果不指定该列的值，则会为默认的值



# 5、JDBC连接mysql

步骤总结：

1.  加载驱动
2.  通过DriverManager类连接数据库
3.  通过数据库对象Connection的对象获得执行sql的对象Statement
4.  获得返回结果集（只有查询sql才有返回）
5.  释放连接

```java
public class JdbcDemo {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        //加载mysql驱动
        Class.forName("com.mysql.jdbc.Driver");

        //创建连接
        String url = "jdbc:mysql://localhost:3306/mydatabase?				 useUnicode=true&characterEncoding=utf8&useSSL=false";
        String user = "root";
        String password = "686995";
        Connection connection = DriverManager.getConnection(url, user, password);

        //创建执行sql的对象 Statement
        Statement statement = connection.createStatement();
        String sql = "";
        for (int i=1; i<=10; i++) {
            sql = "insert into vop_user_info_t values(" + i + ",'chengchao',25)";
            statement.executeUpdate(sql);
            System.out.println("插入第"+i+"条数据");
        }

        //关闭资源
        statement.close();
        connection.close();
    }
}
```

