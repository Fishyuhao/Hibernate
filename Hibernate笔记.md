## Hibernate笔记
* Hibernate是一个持久层的ORM框架(对象关系映射)
* ORM:Object Relational Mapping

### 导入jar包和数据库
 1. 创建数据库cst_customer和表cst_customer
   ```sql
  CREATE TABLE `cst_customer` (
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

#### 持久化类的编写规则
  1. 持久化类：将内存中的一个对象持久化到数据库中的过程。Hibernate框架就是用来进行持久化的框架。
  2. 持久化类：一个java对象与数据库的表建立了映射关系，那么这个类在Hibernate中称为持久化类，持久化类=java类+映射文件
  3. 持久化类的编写规则
   * 对持久化类提供一个无参的构造方法。
   * 属性需要私有化，对私有属性提供public的get和set方法
   * 对持久化类提供一个唯一标识OID与数据库主键对应
   * 持久化类中的属性尽量使用包装类型
   * 持久化类不要使用final进行修饰

#### 主键生成策略
1. 在实际开发中，尽量使用代理主键(与表不相关的字段id)
2. 主键生成策略
 * increment:自动增长机制，适用于short、int、long类型的主键，有线程安全问题。hibernate提供的自动增长机制，会用select max(id) from table;查询出最大的id之后又+1作为主键。
 * identify:适用于适用于short、int、long类型的主键，适用于有自动增长机制的数据量(MySQL,MSSQL),Oracle没有自动增长机制
 * sequence:适用于short、int、long类型的主键，采用序列的方式，Oracle支持，MySQL不支持。
 * uuid:适用于字符串类型的主键，使用hibernate中的随机方式生成字符串主键。
 * native:本地策略，在identify和sequence之间自动切换。
 * assigned:hibernate放弃外键管理，需要通过手动编写程序或用户自己设置
 * foreign:外部的。一对一的一种关联映射的情况下使用(了解);

#### 持久化类的3中状态
1. 瞬时态:没有唯一标识oid，没有Session管理
2. 持久态:有唯一标识oid，被Session管理
3. 脱管态:有唯一标识oid，没有被Session管理
```java
public class HibernateState{
  public void state(){
    @Test
    Session session=HibernateUtils.openSession();
    Transaction transaction=session.beginTransaction();

    Customer customer=new Customer();//瞬时态
    customer.setCust_name("lily");

    Serializable id=session.save(customer);//持久态

    transaction.commit();
    session.close();
    System.out.println("姓名:"+customer.getCust_name());//脱管态

  }
}
```

#### 设置hibernate的隔离级别
* Read uncommitted-1
* Read committed:解决脏读-2
* Repeatable read:解决脏读和不可重复读-4
* Serializable:解决所有问题-3
```xml
<property name="hibernate.connection.isolocation">4</property>
```

#### 线程绑定的Session
1. 改写工具类
```java
public static Session getCurrentSession() {
		return sf.getCurrentSession();
	}
```
2. 配置文件hibernate.cfg.xml
```xml
<!-- 配置当前线程绑定的Session -->
<property name="hibernate.current_session_context_class">thread</property>
```

#### Hibernate的Query
1. 简单查询
```java
public void Query() {
		Session session=HibernateUtils.getCurrentSession();
		Transaction transaction=session.beginTransaction();
		//通过Session获取Query接口
		String hql="from Customer";
		Query query=session.createQuery(hql);
		List<Customer> list=query.list();
		for(Customer customer:list) {
			System.out.println(customer);
		}
		transaction.commit();
	}
```
2. 条件查询
```java
public void Query() {
		Session session=HibernateUtils.getCurrentSession();
		Transaction transaction=session.beginTransaction();
		//通过Session获取Query接口
		String hql="from Customer where cust_name like ?";
		Query query=session.createQuery(hql);
		query.setParameter(0, "c%");//条件查询
		List<Customer> list=query.list();
		for(Customer customer:list) {
			System.out.println(customer);
		}
		transaction.commit();
	}
```
3. 分页查询
```java
public void Query() {
		Session session=HibernateUtils.getCurrentSession();
		Transaction transaction=session.beginTransaction();
		//通过Session获取Query接口
		String hql="from Customer";
		Query query=session.createQuery(hql);
		query.setFirstResult(3);
		query.setMaxResults(3);
		List<Customer> list=query.list();
		for(Customer customer:list) {
			System.out.println(customer);
		}
		transaction.commit();
	}
```

#### Hibernate的Criteria
```java
public void CriteriaQuery() {
		Session session=HibernateUtils.getCurrentSession();
		Transaction tx=session.beginTransaction();
		/*简单查询
		Criteria criteria=session.createCriteria(Customer.class);*/

		/*//条件查询
    Criteria criteria=session.createCriteria(Customer.class);
		criteria.add(Restrictions.like("cust_name", "c%"));*/

		//分页查询
		Criteria criteria=session.createCriteria(Customer.class);
		criteria.setFirstResult(0);
		criteria.setMaxResults(3);

		List<Customer> list=criteria.list();
		for(Customer customer:list) {
			System.out.println(customer);
		}
		tx.commit();
	}
```
#### 一对多查询
* 数据库建表
```sql
CREATE TABLE `cst_customer` (
`cust_id` bigint(32) NOT NULL AUTO_INCREMENT COMMENT '客户编号(主键)',
`cust_name` varchar(32) NOT NULL COMMENT '客户名称(公司名称)',
`cust_source` varchar(32) DEFAULT NULL COMMENT '客户信息来源',
`cust_industry` varchar(32) DEFAULT NULL COMMENT '客户所属行业',
`cust_level` varchar(32) DEFAULT NULL COMMENT '客户级别',
`cust_phone` varchar(64) DEFAULT NULL COMMENT '固定电话',
`cust_mobile` varchar(16) DEFAULT NULL COMMENT '移动电话',
PRIMARY KEY (`cust_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
CREATE TABLE `cst_linkman` (
  `lkm_id` bigint(32) NOT NULL AUTO_INCREMENT COMMENT '联系人编号(主键)',
  `lkm_name` varchar(16) DEFAULT NULL COMMENT '联系人姓名',
  `lkm_cust_id` bigint(32) DEFAULT NULL COMMENT '客户id',
  `lkm_gender` char(1) DEFAULT NULL COMMENT '联系人性别',
  `lkm_phone` varchar(16) DEFAULT NULL COMMENT '联系人办公电话',
  `lkm_mobile` varchar(16) DEFAULT NULL COMMENT '联系人手机',
  `lkm_email` varchar(64) DEFAULT NULL COMMENT '联系人邮箱',
  `lkm_qq` varchar(16) DEFAULT NULL COMMENT '联系人qq',
  `lkm_position` varchar(16) DEFAULT NULL COMMENT '联系人职位',
  `lkm_memo` varchar(512) DEFAULT NULL COMMENT '联系人备注',
  PRIMARY KEY (`lkm_id`),
  KEY `FK_cst_linkman_lkm_cust_id` (`lkm_cust_id`),
  CONSTRAINT `FK_cst_linkman_lkm_cust_id` FOREIGN KEY (`lkm_cust_id`) REFERENCES `cst_customer` (`cust_id`) ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```
* 加载映射文件:hibernate.cfg.xml
```xml
<mapping resource="com/itheima/hibernate/domain/Customer.hbm.xml"/>
<mapping resource="com/itheima/hibernate/domain/LinkMan.hbm.xml"/>
```
* Customer.java
```java
public class Customer {
			private Long cust_id;
			private String cust_name;
			private String cust_source;
			private String cust_industry;
			private String cust_level;
			private String cust_phone;
			private String cust_mobile;
			private Set<LinkMan> linkMans=new HashSet<LinkMan>();//关联多个联系人
    }
```
* LinkMan.java
```java
public class LinkMan {
	private Long lkm_id;
	private String lkm_name;
	private char lkm_gender;
	private String lkm_phone;
	private String lkm_mobile;
	private String lkm_email;
	private String lkm_qq;
	private String lkm_position;
	private String lkm_memo;
	private Customer customer;//关联一个顾客
}
```
* Customer.hbm.xml
```xml
<hibernate-mapping>
<!--建立类与表之间的映射-->
<class name="com.itheima.hibernate.domain.Customer" table="cst_customer">
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
	<!--
		set标签:
			name:多的一方的对象集合属性名称
			key:column多的一方的外键名
	 -->
	<set name="linkMans" cascade="save-update" inverse="true">
		<key column="lkm_cust_id"/>
		<one-to-many class="com.itheima.hibernate.domain.LinkMan"/>
	</set>
</class>
</hibernate-mapping>
```
* LinkMan.hbm.xml
```xml
<hibernate-mapping>
	<class name="com.itheima.hibernate.domain.LinkMan" table="cst_linkman">
		<id name="lkm_id" column="lkm_id">
			<generator class="native"/>
		</id>
		<property name="lkm_name" column="cust_name"></property>
		<property name="lkm_gender" column="lkm_gender"></property>
		<property name="lkm_phone" column="lkm_phone"></property>
		<property name="lkm_mobile" column="lkm_mobile"></property>
		<property name="lkm_email" column="lkm_email"></property>
		<property name="lkm_qq" column="lkm_qq"></property>
		<property name="lkm_position" column="lkm_position"></property>
		<property name="lkm_memo" column="lkm_memo"></property>
		<!--
			many-to-one标签
			name:一的一方的对象属性名
			class:一的一方的类全路径
			column:多的一方的外键
		 -->
		<many-to-one name="customer" class="com.itheima.hibernate.domain.Customer" column="lkm_cust_id"/>
	</class>
</hibernate-mapping>
```
* 测试方法
```java
@Test
	public void oneToMany() {
		Session session=HibernateUtils.getCurrentSession();
		Transaction tx=session.beginTransaction();
		//创建2个顾客
		Customer customer1=new Customer();
		customer1.setCust_name("Jobs");
		Customer customer2=new Customer();
		customer2.setCust_name("Hub");
		//创建3个联系人
		LinkMan linkMan1=new LinkMan();
		linkMan1.setLkm_name("Alec");
		LinkMan linkMan2=new LinkMan();
		linkMan2.setLkm_name("Allen");
		LinkMan linkMan3=new LinkMan();
		linkMan3.setLkm_name("Bobby");
		//设置关系
		linkMan1.setCustomer(customer1);
		linkMan2.setCustomer(customer1);
		linkMan3.setCustomer(customer2);
		customer1.getLinkMans().add(linkMan1);
		customer1.getLinkMans().add(linkMan2);
		customer2.getLinkMans().add(linkMan3);
		session.save(linkMan1);
		session.save(linkMan2);
		session.save(linkMan3);
		session.save(customer1);
		session.save(customer2);

		tx.commit();
	}
```
