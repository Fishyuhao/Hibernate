## Hibernate笔记
* Hibernate是一个持久层的ORM框架(对象关系映射)
* ORM:Object Relational Mapping

### 导入jar包和数据库
 1. 创建数据库cst_customer和表cst_customer
   ```sql
   cst_customerCREATE TABLE `cst_customer` (
  `cust_id` bigint(32) NOT NULL AUTO_INCREMENT COMMENT '客户编号(主键)',
  `cust_name` varchar(32) NOT NULL COMMENT '客户名称(公司名称)',
  `cust_source` varchar(32) DEFAULT NULL COMMENT '客户信息来源',
  `cust_industry` varchar(32) DEFAULT NULL COMMENT '客户所属行业',
  `cust_level` varchar(32) DEFAULT NULL COMMENT '客户级别',
  `cust_phone` varchar(64) DEFAULT NULL COMMENT '固定电话',
  `cust_mobile` varchar(16) DEFAULT NULL COMMENT '移动电话',
  PRIMARY KEY (`cust_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
   ```

### 创建Java类Customer
 1. 创建Customer与表cst_customer中的字段一一对应
 ```java
 public class Customer {
		private Long cust_id;
		private String cust_name;
		private String cust_source;
		private String cust_industry;
		private String cust_level;
		private String cust_phone;
		private String cust_mobile;
  }
 ```

### 创建映射文件Customer.hbm.xml
 ```xml
 <?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
<!--建立类与表之间的映射-->
<class name="com.itheima.hibernate.demo01.Customer" table="cst_customer">
	<!-- 建立类中属性与表中主键的映射 -->
	<id name="cust_id" column="cust_id">
		<generator class="native"/>
	</id>
	<!-- 建立类中普通属性与表的字段的映射 -->
	<property name="cust_name" column="cust_name"></property>
	<property name="cust_source" column="cust_source"></property>
	<property name="cust_industry" column="cust_industry"></property>
	<property name="cust_level" column="cust_level"></property>
	<property name="cust_phone" column="cust_phone"></property>
	<property name="cust_mobile" column="cust_mobile"></property>
</class>
</hibernate-mapping>
 ```

### 创建核心配置文件hibernate.cfg.xml
 ```xml
 <?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
	"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
	"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
	<session-factory>
		<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
		<property name="hibernate.connection.url">jdbc:mysql://localhost:3306/cst_customer?serverTimezone=UTC</property>
		<property name="hibernate.connection.username">root</property>
		<property name="hibernate.connection.password">sunday</property>

		<!-- 配置hibernate的方言 -->
		<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>

        <!-- 打印sql语句 -->
		<property name="hibernate.show_sql">true</property>
		<!-- 格式化sql语句 -->
		<property name="hibernate.format_sql">true</property>

		<!-- 加载映射文件 -->
		<mapping resource="com/itheima/hibernate/demo01/Customer.hbm.xml"/>

	</session-factory>
</hibernate-configuration>
 ```

### 测试类
 ```java
 public class HibernateDemo01 {
	@Test
	public void demo01() {
		//加载配置文件
		Configuration configuration=new Configuration().configure();
		//创建一个SessionFactory对象
		SessionFactory sessionFactory=configuration.buildSessionFactory();
		//通过SessionFactory获取Session对象
		Session session=sessionFactory.openSession();
		//手动开启事务
		Transaction transaction=session.beginTransaction();
		//测试代码
		Customer customer=new Customer();
		customer.setCust_name("CR7");
		session.save(customer);
		//事务提交
		transaction.commit();
		//资源释放
		session.close();
	}
}
 ```

### hibernate的映射配置
#### 映射的配置
 * class标签的配置:标签用来建立类与表之间的映射关系
   * 属性:
     * name:类的全路径。
     * table:表名(类名与表名一致,table可以省略)
     * catalog:数据库名
 * id标签的配置:标签用来建立类中的属性与表中的主键的映射关系
   * 属性:
     * name:类中的属性名
     * column:表中的字段名(类中的属性名和表中的字段名一致,column可以省略)
     * length:长度
     * type:类型
  * property标签的配置:标签用来建立类中的属性与表中字段的映射关系
    * 属性
      * name:类中的属性名
      * column:表中的字段名
      * length:长度
      * type:类型
      * not-null:设置非空属性
      * unique:设置唯一

#### 核心配置hibernate.cfg.xml
 * 必须的配置
   * 连接数据库的基本参数
    ```xml
    <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
		<property name="hibernate.connection.url">jdbc:mysql://localhost:3306/cst_customer?serverTimezone=UTC</property>
		<property name="hibernate.connection.username">root</property>
		<property name="hibernate.connection.password">sunday</property>
    ```
    * 方言
    ```xml
    <!-- 配置hibernate的方言 -->
		<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
    ```
    * 可选的配置
     * 打印sql:
     ```xml
     <property name="hibernate.show_sql">true</property>
     ```
     * 格式化sql
     ```xml
     <!-- 格式化sql语句 -->
		<property name="hibernate.format_sql">true</property>
     ```
     * 自动建表
     ```xml
     <!-- 自动建表 -->
		<property name="hibernate.hbm2ddl.auto">create</property>
     ```
       * none:不使用hibernate的自动建表
       * create:如果数据库中已经有表了，删除原有表，重新创建，如果没有表，新建表(测试)
       * create-drop:如果数据库中已经有表了，删除原有表，执行操作后删除这个表，如果没有表，新建一个表，使用完了删除这个表(测试)，必须关闭工厂:sessionFactory.close();
       * update:如果数据库中有表，使用原有表，如果没有表，创建新表(更新表结构)
       * validate:如果没有表，不会创建，只会使用数据库中原有的表(校验映射和表结构)
      * 加载映射文件
      ```xml
      <!-- 加载映射文件 -->
		<mapping resource="com/itheima/hibernate/demo01/Customer.hbm.xml"/>
      ```

### hibernate的核心API
#### Configuration:hibernate的配置对象
* 加载核心配置文件
 * hibernate.properties
 ```java
 Configuration configuration=new Configuration()
 ```
* hibernate.cfg.xml
```java
Configuration configuration=new Configuration().configure();
//手动加载映射
configuration.addResource("com/itheima/hibernate/demo01/Customer.hbm.xml");
```
log4j.properties
```
# error warn info debug trace(级别)
log4j.rootLogger= info, stdout(输出源)
```

#### hibernate的SessionFactory对象
抽取工具类，获得Session对象
```java
public class HibernateUtils {
	public static final Configuration cfg;
	public static final SessionFactory sf;
	static {
		//加载配置文件
		cfg=new Configuration().configure();
		//创建一个SessionFactory对象
		sf=cfg.buildSessionFactory();
	}
	public static Session openSession() {
		return sf.openSession();
	}
}
```
#### Session对象
Session代表的是Hibernate与数据库的连接对象，不是线程安全的，所以要定义成局部变量。
* Session中API
 * 保存方法
   * Serializable save(Object obj):返回一个id
 * 查询方法
   * T get(Class c,Serializable id);
   ```
   采用的是立即加载，执行到Customer customer=session.get(Customer.class,1L);就会发送SQL语句，查询后返回的是真实对象本身，查询一个不存在的对象会返回null
   ```
   * T load(Class c,Serializable id);
    ```
    采用的是延迟加载(lazy懒加载),执行到Customer customer=session.load(Customer.class,1L);不会发送SQL语句，查询后返回的是代理对象，查询一个不存在的对象会返回ObjectNotFoundException
    ```
  * 修改方法
  ```java
  public void update() {
		Session session=HibernateUtils.openSession();
		Transaction transaction=session.beginTransaction();
		/*//直接创建对象进行修改
		Customer customer=new Customer();
		customer.setCust_id(1l);
		customer.setCust_name("Loner");
		session.update(customer);*/
		//先查询再修改,只会更新修改的值，不会影响其他值
		Customer customer=session.get(Customer.class,1l);
		customer.setCust_name("amy");
		session.update(customer);
		transaction.commit();
		session.close();
	}
  ```
  * 删除方法
  ```java
  @Test
	public void delete() {
		Session session=HibernateUtils.openSession();
		Transaction transaction=session.beginTransaction();
		/*//直接删除对象
		Customer customer=new Customer();
		customer.setCust_id(1l);
		session.delete(customer);*/
		//先查询再删除
		Customer customer=session.get(Customer.class, 3l);
		session.delete(customer);
		transaction.commit();
		session.close();
	}
  ```
  * 查询所有
  ```java
  public void query() {
		Session session=HibernateUtils.openSession();
		Transaction transaction=session.beginTransaction();
		/*//接受HQl:Hinernate Query Language 面向对象的查询语句
		Query query =session.createQuery("from Customer");
		List<Customer> list=query.list();
		for(Customer customer:list) {
			System.out.println(customer);
		}*/
		//接受SQL语句
		SQLQuery query=session.createSQLQuery("select * from cst_customer");
		List<Object[]> list=query.list();
		for(Object[] objects:list) {
			System.out.println(Arrays.toString(objects));
		}
		transaction.commit();
		session.close();
	}
  ```
