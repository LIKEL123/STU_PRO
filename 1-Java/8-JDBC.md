# JDBC

```properties
jdbc是Java提供一套用来连接数据库的接口。
```

 

**1-通过Driver和DriverManger获取连接对象**

**2-通过properties文件进行传入参数，在通过properties对象的getproperty去获取传参的值**

```java

/*
    JDBC : 是Java提供的一套用来操作数据库的接口

    Java代码操作数据库：
        1.保证MySQL是可以正常使用的（服务是否开启）
        2.保证账号密码错误
        3.导入MySQL驱动包（一定要注意版本）

 */
public class JDBCDemo {

    @Test
    public void test() throws Exception {
        //1.获取Driver类的对象
        Driver driver = new com.mysql.jdbc.Driver();
        //2.获取Connection连接对象
        /*
            connect(String url, java.util.Properties info)
            url : 是mysql连接地址
                jdbc:mysql://localhost:3306/atguigu
                jdbc:mysql: 协议
                localhost ：mysql服务器的地址
                3306 ：端口号
                atguigu ：数据库的名字
             info : 用来存放连接mysql的数据（账号和密码）
         */
        String url = "jdbc:mysql://localhost:3306/myemployees";
        Properties properties = new Properties();
        //账号
        //properties.put("user","root");//key值不能随便写只能是user
        //properties.put("password","123321");//key值不能随便写只能是password
        properties.setProperty("user","root");
        properties.setProperty("password","123321");

        Connection connect = driver.connect(url, properties);

        System.out.println(connect);

    }

    /*
        方式二：
        通过使用DriverManager获取数据库连接对象
     */
    @Test
    public void test2() throws SQLException {
        Driver driver = new com.mysql.jdbc.Driver();
        //将driver注册到DriverManager中
        DriverManager.registerDriver(driver);
        //通过DriverManager获取数据库连接对象
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/myemployees",
                "root", "123321");

        System.out.println(connection);
    }

    /*
        在方式二的基础上再进行优化
     */
    @Test
    public void test3() throws Exception {
        //1.只要让com.mysql.jdbc.Driver进行类加载那么就可以直接通过DriverManager获取连接对象
        Class.forName("com.mysql.jdbc.Driver");
        //获取数据库连接对象
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/myemployees",
                "root", "123321");
        System.out.println(connection);
    }

    /*
        终级版
     */
    @Test
    public void test4() throws Exception {
        String driverClass = "";
        String url = "";
        String username = "";
        String password = "";

        //通过Properties读取内容
        //1.创建Properties对象
        Properties p = new Properties();
        //2.加载流
        FileInputStream fis = new FileInputStream("jdbc.properties");
        p.load(fis);
        driverClass = p.getProperty("driverClass");
        url = p.getProperty("url");
        username = p.getProperty("username");
        password = p.getProperty("password");
        //3.关资源
        fis.close();

        Class.forName(driverClass);
        //获取数据库连接对象
        Connection connection = DriverManager.getConnection(url,username,password);
        System.out.println(connection);
    }
}

class Person{
    static {
        System.out.println("person类加载了");
    }
}

```

小记：

```properties
1. properties是Hashtable类的子类
2. Properties中的key和value都是String类型
3. 可以通过Properties去读取配置文件中的内容
```

