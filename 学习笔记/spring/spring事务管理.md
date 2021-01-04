# Spring事务管理

## Spring事务的特性(ACID)

[事务管理链接](https://blog.csdn.net/trigl/article/details/50968079)



	* 原子性：指的一个事务里面做的一系列操作，要么全部成功要么全部失败
	* 一致性：指在一个事务里面，“有失必有得”，不会是部分操作了数据，另一个操作没有接受这部分数据
	* 隔离性：操作同一批数据时，各个事务都是独立的（行锁，表锁）
	* 持久性：指的事务不管是成功还是失败，数据不会受到影响，会持久性的写入到储存器中

## 核心接口

​	![image-20201119182306240](C:\Users\kgcaox\AppData\Roaming\Typora\typora-user-images\image-20201119182306240.png)



### 事务管理器

PlatformTransactionManager接口：

```java
Public interface PlatformTransactionManager()...{  
    // 由TransactionDefinition得到TransactionStatus对象
    TransactionStatus getTransaction(TransactionDefinition definition) throws TransactionException; 
    // 提交
    Void commit(TransactionStatus status) throws TransactionException;  
    // 回滚
    Void rollback(TransactionStatus status) throws TransactionException;  
    } 
```

### 基础事务的常用属性

​			上面讲到的事务管理器接口PlatformTransactionManager通过getTransaction(TransactionDefinition definition)方法来得到事务，这个方法里面的参数		是TransactionDefinition类，这个类就定义了一些基本的事务属性。

```java
public interface TransactionDefinition {
    int getPropagationBehavior(); // 返回事务的传播行为
    int getIsolationLevel(); // 返回事务的隔离级别，事务管理器根据它来控制另外一个事务可以看到本事务内的哪些数据
    int getTimeout();  // 返回事务必须在多少秒内完成
    boolean isReadOnly(); // 事务是否只读，事务管理器能够根据这个返回值进行优化，确保事务是只读的
} 
```

#### 传播行为（七种）

   * 保证在同一个事务
     1. 支持当前事务，若没有事务，则新建（propagion_required默认事务级别）
     2. 支持当前事务，若没有事务，则不使用事务
     3. 支持当前事务，若没有事务，则抛出异常
* 保证不在同一个事务
  4. 支持当前事务，若当前有事务存在，则挂起，反之创建一个新事务
  
     ```sq
     //事务属性 PROPAGATION_REQUIRED
     methodA(){
         doSomeThingA();
         methodB();
         doSomeThingB();
     }
     
     //事务属性 PROPAGATION_REQUIRES_NEW
     methodB(){
         ……
     }
     
     
     相当于
     main(){
         TransactionManager tm = null;
         try{
             //获得一个JTA事务管理器
             tm = getTransactionManager();
             tm.begin();//开启一个新的事务
             Transaction ts1 = tm.getTransaction();
             doSomeThing();
             tm.suspend();//挂起当前事务
             try{
                 tm.begin();//重新开启第二个事务
                 Transaction ts2 = tm.getTransaction();
                 methodB();
                 ts2.commit();//提交第二个事务
             } Catch(RunTimeException ex) {
                 ts2.rollback();//回滚第二个事务
             } finally {
                 //释放资源
             }
             //methodB执行完后，恢复第一个事务
             tm.resume(ts1);
             doSomeThingB();
             ts1.commit();//提交第一个事务
         } catch(RunTimeException ex) {
             ts1.rollback();//回滚第一个事务
         } finally {
             //释放资源
         }
     }
     ```
  
     
  
  5. 用非事务的方式运行，若存在事务，则抛出异常
  
  6. 用非事务的方式运行，若存在事务，则挂起当前事务
  
  7. 若当前事务存在，则以嵌套事务执行

#### 		隔离级别

* 使用数据库默认的隔离级别（默认隔离级别）
* 读已提交（Oracle默认）
* 读未提交
* 可重复读（Mysql默认）
* 串行化



#### 		只读

​		概念：在只读事务申明直到事务结束，后面其他操作做得数据修改，该事务是看不到的，当事务被标识为只读事务时，Spring可以对某些可以针对只读事务进行优化的资源就可以执行相应的优化措施。

​		应用场合：

​			如果你一次执行单条查询语句，则没有必要启用事务支持，数据库默认支持SQL执行期间的读一致性； 
​		如果你一次执行多条查询语句，例如统计查询，报表查询，在这种场景下，多条查询SQL必须保证整体的读一致性，否则，在前条SQL查询之后，后条SQL查		询之前，数据被其他用户改变，则该次整体的统计查询将会出现读数据不一致的状态，此时，应该启用事务支持。
​		【注意是一次执行多次查询来统计某些信息，这时为了保证数据整体的一致性，要用只读事务】

​		如何设置只读事务：

​			对于只读查询，可以指定事务类型为readonly，即只读事务。

​			由于只读事务不存在数据的修改，因此数据库将会为只读事务提供一些优化手段，例如Oracle对于只读事务，不启动回滚段，不记录回滚log。

```
 (1)JDBC中，指定只读事务的办法为： connection.setReadOnly(true);
 (2)在Hibernate中，指定只读事务的办法为： session.setFlushMode(FlushMode.NEVER),此时，Hibernate也会为只读事务提供Session方面的一些优化手段; 	
 (3在Spring的Hibernate封装中，指定只读事务的办法为： bean配置文件中，prop属性增加“readOnly”或者用注解方式@Transactional(readOnly=true)
```



#### 		事务超时

​		概念：为防止事务执行时间过长，占用不必要的数据库资源，当事务在规定的时间内未执行完毕，则事务需要进行回滚，释放数据库资源

​		那么问题来了，怎么判断事务是否超时的标准呢

```java
测试代码：
@RunWith(SpringJUnit4ClassRunner.class)  
@ContextConfiguration(locations = "classpath:spring-config.xml")  
@TransactionConfiguration(transactionManager = "txManager", defaultRollback = false)  
@Transactional(timeout = 2)  
public class Timeout1Test {  
    @Autowired  
    private DataSource ds;  
    @Test  
    public void testTimeout() throws InterruptedException {  
        System.out.println(System.currentTimeMillis());  
        JdbcTemplate jdbcTemplate = new JdbcTemplate(ds);  
        jdbcTemplate.execute(" update test set name = name || '1'");  
        System.out.println(System.currentTimeMillis());  
        Thread.sleep(3000L);  
    }  
}  
```

​		上面代码，发现其并没有超时，为什么？

```java
//doBegin方法
public class DataSourceTransactionManager(){
…………  
int timeout = determineTimeout(definition);  
if (timeout != TransactionDefinition.TIMEOUT_DEFAULT) {  
    txObject.getConnectionHolder().setTimeoutInSeconds(timeout);  
}  
}

//determineTimeout用来获取我们设置的事务超时时间；然后设置到ConnectionHolder对象上（其是ResourceHolder子类），接着看ResourceHolderSupport的setTimeoutInSeconds实现：
public class ResourceHolderSupport（）{
    public void setTimeoutInSeconds(int seconds) {  
    setTimeoutInMillis(seconds * 1000);  
	}  
  
	public void setTimeoutInMillis(long millis) {  
    this.deadline = new Date(System.currentTimeMillis() + millis);  
	}  
}

//其会设置一个deadline时间；用来判断事务超时时间的
public int getTimeToLiveInSeconds() {  
    double diff = ((double) getTimeToLiveInMillis()) / 1000;  
    int secs = (int) Math.ceil(diff);  
    checkTransactionTimeout(secs <= 0);  
    return secs;  
}  
  
public long getTimeToLiveInMillis() throws TransactionTimedOutException{  
    if (this.deadline == null) {  
        throw new IllegalStateException("No timeout specified for this resource holder");  
    }  
    long timeToLive = this.deadline.getTime() - System.currentTimeMillis();  
    checkTransactionTimeout(timeToLive <= 0);  
    return timeToLive;  
}  
private void checkTransactionTimeout(boolean deadlineReached) throws TransactionTimedOutException {  
    if (deadlineReached) {  
        setRollbackOnly();  
        throw new TransactionTimedOutException("Transaction timed out: deadline was " + this.deadline);  
    }  
}  

//会发现在调用getTimeToLiveInSeconds和getTimeToLiveInMillis，会检查是否超时，如果超时设置事务回滚，并抛出TransactionTimedOutException异常。到此我们只要找到调用它们的位置就好了，那什么地方调用的它们呢？ 最简单的办法使用如“IntelliJ IDEA”中的“Find Usages”找到get***的使用地方；会发现：

DataSourceUtils.applyTransactionTimeout会调用DataSourceUtils.applyTimeout
    
public static void applyTimeout(Statement stmt, DataSource dataSource, int timeout) throws SQLException {  
    Assert.notNull(stmt, "No Statement specified");  
    Assert.notNull(dataSource, "No DataSource specified");  
    ConnectionHolder holder = (ConnectionHolder) TransactionSynchronizationManager.getResource(dataSource);  
    if (holder != null && holder.hasTimeout()) {  
        // Remaining transaction timeout overrides specified value.  
        stmt.setQueryTimeout(holder.getTimeToLiveInSeconds());  
    }  
    else if (timeout > 0) {  
        // No current transaction timeout -> apply specified value.  
        stmt.setQueryTimeout(timeout);  
    }  
} 

其中其在stmt.setQueryTimeout(holder.getTimeToLiveInSeconds());中会调用getTimeToLiveInSeconds，此时就会检查事务是否超时；然后在JdbcTemplate中，执行sql之前，会调用其applyStatementSettings：其会调用DataSourceUtils.applyTimeout(stmt, getDataSource(), getQueryTimeout());设置超时时间；具体可以看其源码；
```

结论：Spring事务超时 = 事务开始时到最后一个Statement创建时时间 + 最后一个Statement的执行时超时时间（即其queryTimeout）

#### 		回滚规则

​		默认情况下，事务只有在运行时异常时，才会进行回滚。在遇到检查时异常不会进行回滚，或者申明特定的异常进行回滚

> 手动回滚事务
>
> > 1. 手动回滚事务（碰到异常或者触发特定的业务条件）
> >
> > ```java
> > @Transactional(rollbackFor = Exception.class)
> > @Override
> > public void saveEntity() throws Exception{
> >     try {
> >         userDao.saveUser();
> >         studentDao.saveStudent();
> >     }catch (Exception e){
> >         System.out.println("异常了=====" + e);
> >         //手动强制回滚事务，这里一定要第一时间处理
> >         TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
> >     }
> > }
> > ```
> >
> > 2. 回滚部分异常（设置回滚点）
> >
> >    ```java
> >    @Transactional(rollbackFor = Exception.class)
> >    @Override
> >    public void saveEntity() throws Exception{
> >        Object savePoint = null;
> >        try {
> >            userDao.saveUser();
> >            //设置回滚点
> >            savePoint = TransactionAspectSupport.currentTransactionStatus().createSavepoint();
> >            studentDao.saveStudent(); //执行成功
> >            int a = 10/0; //这里因为除数0会报异常,进入catch块
> >        }catch (Exception e){
> >            System.out.println("异常了=====" + e);
> >            //手工回滚异常
> >            TransactionAspectSupport.currentTransactionStatus().rollbackToSavepoint(savePoint);
> >        }
> >    }
> >    ```
> >
> > 3. ### DataSourceTransactionManager（使用手动的方式管理事务）
> >
> >    ```java
> >    @Autowired
> >    DataSourceTransactionManager dataSourceTransactionManager;
> >    @Autowired
> >    TransactionDefinition transactionDefinition;
> >    
> >    手动开启事务
> >    TransactionStatus transactionStatus = dataSourceTransactionManager.getTransaction(transactionDefinition);
> >    手动提交事务
> >    dataSourceTransactionManager.commit(transactionStatus);//提交
> >    手动回滚事务
> >    dataSourceTransactionManager.rollback(transactionStatus);//最好是放在catch 里面,防止程序异常而事务一直卡在哪里未提交
> >    ```

#### 		事务状态

​		上面讲到的调用PlatformTransactionManager接口的getTransaction()的方法得到的是TransactionStatus接口的一个实现，这个接口的内容如下：

```sql
public interface TransactionStatus{
    boolean isNewTransaction(); // 是否是新的事物
    boolean hasSavepoint(); // 是否有恢复点
    void setRollbackOnly();  // 设置为只回滚
    boolean isRollbackOnly(); // 是否为只回滚
    boolean isCompleted; // 是否已完成
} 
```

## 编程式事务

​		Spring提供的编程式事务，能够更加准确得定义事务边界，让业务操作变得更加的灵活。而申明式事务，是通过AOP的方式（通过注解的方式，类似多数据源的配置），将事务和业务代码进行解耦。		

> 编程式事务的实现
>
> ​		Spring提供两种方式的编程式事务管理，分别是：使用TransactionTemplate和直接使用PlatformTransactionManager。源码中，对将PlatformTransactionManager作为参数，传入到TransactionTemplate。
>
> > ##### TransactionTemplate的实现
> >
> > 采用TransactionTemplate和采用其他Spring模板，如JdbcTempalte和HibernateTemplate是一样的方法。它使用回调方法，把应用程序从处理取得和释放资源中解脱出来。如同其他模板，TransactionTemplate是线程安全的。代码片段：
> >
> > ```java
> > TransactionTemplate tt = new TransactionTemplate(); // 新建一个TransactionTemplate
> >     Object result = tt.execute(
> >         new TransactionCallback(){  
> >             public Object doTransaction(TransactionStatus status){  
> >                 updateOperation();  
> >                 return resultOfUpdateOperation();  
> >             }  
> >     }); // 执行execute方法进行事务管理
> > ```
> >
> > ------------
> >
> > 使用TransactionCallback()可以返回一个值。如果使用TransactionCallbackWithoutResult则没有返回值。TransactionCallbackWithoutResult是作为一个抽象类来实现TransactionCallback接口
> >
> > ```
> > public abstract class TransactionCallbackWithoutResult implements TransactionCallback<Object> {
> >     public TransactionCallbackWithoutResult() {
> >     }
> > 
> >     public final Object doInTransaction(TransactionStatus status) {
> >         this.doInTransactionWithoutResult(status);
> >         return null;
> >     }
> > 
> >     protected abstract void doInTransactionWithoutResult(TransactionStatus var1);
> > }
> > 
> > ```
> >
> > ##### PlatformTransactionManager的实现
> >
> > ```java
> > DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager(); //定义一个某个框架平台的TransactionManager，如JDBC、Hibernate
> >     dataSourceTransactionManager.setDataSource(this.getJdbcTemplate().getDataSource()); // 设置数据源
> >     DefaultTransactionDefinition transDef = new DefaultTransactionDefinition(); // 定义事务属性
> >     transDef.setPropagationBehavior(DefaultTransactionDefinition.PROPAGATION_REQUIRED); // 设置传播行为属性
> >     TransactionStatus status = dataSourceTransactionManager.getTransaction(transDef); // 获得事务状态
> >     try {
> >         // 数据库操作
> >         dataSourceTransactionManager.commit(status);// 提交
> >     } catch (Exception e) {
> >         dataSourceTransactionManager.rollback(status);// 回滚
> >     }
> > ```

## 声明式事务

​		由Spring提供的默认事务，有五种配置的方式（四配置一注解）。

	 (1)每个Bean都有一个代理
	
		 <?xml version="1.0" encoding="UTF-8"?>
		 <beans xmlns="http://www.springframework.org/schema/beans"
		     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		     xmlns:context="http://www.springframework.org/schema/context"
		     xmlns:aop="http://www.springframework.org/schema/aop"
		     xsi:schemaLocation="http://www.springframework.org/schema/beans
		            http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
		            http://www.springframework.org/schema/context
		            http://www.springframework.org/schema/context/spring-context-2.5.xsd
		            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-2.5.xsd">
		 
		     <bean id="sessionFactory" 
		             class="org.springframework.orm.hibernate3.LocalSessionFactoryBean"> 
		         <property name="configLocation" value="classpath:hibernate.cfg.xml" /> 
		         <property name="configurationClass" value="org.hibernate.cfg.AnnotationConfiguration" />
		     </bean> 
		 
		     <!-- 定义事务管理器（声明式的事务） --> 
		     <bean id="transactionManager"
		         class="org.springframework.orm.hibernate3.HibernateTransactionManager">
		         <property name="sessionFactory" ref="sessionFactory" />
		     </bean>
		 
		     <!-- 配置DAO -->
		     <bean id="userDaoTarget" class="com.bluesky.spring.dao.UserDaoImpl">
		         <property name="sessionFactory" ref="sessionFactory" />
		     </bean>
		 
		     <bean id="userDao" 
		         class="org.springframework.transaction.interceptor.TransactionProxyFactoryBean"> 
		            <!-- 配置事务管理器 --> 
		            <property name="transactionManager" ref="transactionManager" />    
		         <property name="target" ref="userDaoTarget" /> 
		          <property name="proxyInterfaces" value="com.bluesky.spring.dao.GeneratorDao" />
		         <!-- 配置事务属性 --> 
		         <property name="transactionAttributes"> 
		             <props> 
		                 <prop key="*">PROPAGATION_REQUIRED</prop>
		             </props> 
		         </property> 
		     </bean> 
		 </beans>
	
		
		 (2)所有Bean共享一个代理基类
		
		 <?xml version="1.0" encoding="UTF-8"?>
		 <beans xmlns="http://www.springframework.org/schema/beans"
		     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		     xmlns:context="http://www.springframework.org/schema/context"
		     xmlns:aop="http://www.springframework.org/schema/aop"
		     xsi:schemaLocation="http://www.springframework.org/schema/beans
		            http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
		            http://www.springframework.org/schema/context
		            http://www.springframework.org/schema/context/spring-context-2.5.xsd
		            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-2.5.xsd">
		 
		     <bean id="sessionFactory" 
		             class="org.springframework.orm.hibernate3.LocalSessionFactoryBean"> 
		         <property name="configLocation" value="classpath:hibernate.cfg.xml" /> 
		         <property name="configurationClass" value="org.hibernate.cfg.AnnotationConfiguration" />
		     </bean> 
		 
		     <!-- 定义事务管理器（声明式的事务） --> 
		     <bean id="transactionManager"
		         class="org.springframework.orm.hibernate3.HibernateTransactionManager">
		         <property name="sessionFactory" ref="sessionFactory" />
		     </bean>
		 
		     <bean id="transactionBase" 
		             class="org.springframework.transaction.interceptor.TransactionProxyFactoryBean" 
		             lazy-init="true" abstract="true"> 
		         <!-- 配置事务管理器 --> 
		         <property name="transactionManager" ref="transactionManager" /> 
		         <!-- 配置事务属性 --> 
		         <property name="transactionAttributes"> 
		             <props> 
		                 <prop key="*">PROPAGATION_REQUIRED</prop> 
		             </props> 
		         </property> 
		     </bean>   
		 
		     <!-- 配置DAO -->
		     <bean id="userDaoTarget" class="com.bluesky.spring.dao.UserDaoImpl">
		         <property name="sessionFactory" ref="sessionFactory" />
		     </bean>
		 
		     <bean id="userDao" parent="transactionBase" > 
		         <property name="target" ref="userDaoTarget" />  
		     </bean>
		 </beans>
	
		 (3)使用拦截器
		
		 <?xml version="1.0" encoding="UTF-8"?>
		 <beans xmlns="http://www.springframework.org/schema/beans"
		     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		     xmlns:context="http://www.springframework.org/schema/context"
		     xmlns:aop="http://www.springframework.org/schema/aop"
		     xsi:schemaLocation="http://www.springframework.org/schema/beans
		            http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
		            http://www.springframework.org/schema/context
		            http://www.springframework.org/schema/context/spring-context-2.5.xsd
		            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-2.5.xsd">
		 
		     <bean id="sessionFactory" 
		             class="org.springframework.orm.hibernate3.LocalSessionFactoryBean"> 
		         <property name="configLocation" value="classpath:hibernate.cfg.xml" /> 
		         <property name="configurationClass" value="org.hibernate.cfg.AnnotationConfiguration" />
		     </bean> 
		 
		     <!-- 定义事务管理器（声明式的事务） --> 
		     <bean id="transactionManager"
		         class="org.springframework.orm.hibernate3.HibernateTransactionManager">
		         <property name="sessionFactory" ref="sessionFactory" />
		     </bean> 
		 
		     <bean id="transactionInterceptor" 
		         class="org.springframework.transaction.interceptor.TransactionInterceptor"> 
		         <property name="transactionManager" ref="transactionManager" /> 
		         <!-- 配置事务属性 --> 
		         <property name="transactionAttributes"> 
		             <props> 
		                 <prop key="*">PROPAGATION_REQUIRED</prop> 
		             </props> 
		         </property> 
		     </bean>
		 
		     <bean class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator"> 
		         <property name="beanNames"> 
		             <list> 
		                 <value>*Dao</value>
		             </list> 
		         </property> 
		         <property name="interceptorNames"> 
		             <list> 
		                 <value>transactionInterceptor</value> 
		             </list> 
		         </property> 
		     </bean> 
		 
		     <!-- 配置DAO -->
		     <bean id="userDao" class="com.bluesky.spring.dao.UserDaoImpl">
		         <property name="sessionFactory" ref="sessionFactory" />
		     </bean>
		 </beans>
		
		
		 (4)使用tx标签配置的拦截器
		
		
		 <?xml version="1.0" encoding="UTF-8"?>
		 <beans xmlns="http://www.springframework.org/schema/beans"
		     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		     xmlns:context="http://www.springframework.org/schema/context"
		     xmlns:aop="http://www.springframework.org/schema/aop"
		     xmlns:tx="http://www.springframework.org/schema/tx"
		     xsi:schemaLocation="http://www.springframework.org/schema/beans
		            http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
		            http://www.springframework.org/schema/context
		            http://www.springframework.org/schema/context/spring-context-2.5.xsd
		            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-2.5.xsd
		            http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">
		 
		     <context:annotation-config />
		     <context:component-scan base-package="com.bluesky" />
		 
		     <bean id="sessionFactory" 
		             class="org.springframework.orm.hibernate3.LocalSessionFactoryBean"> 
		         <property name="configLocation" value="classpath:hibernate.cfg.xml" /> 
		         <property name="configurationClass" value="org.hibernate.cfg.AnnotationConfiguration" />
		     </bean> 
		 
		     <!-- 定义事务管理器（声明式的事务） --> 
		     <bean id="transactionManager"
		         class="org.springframework.orm.hibernate3.HibernateTransactionManager">
		         <property name="sessionFactory" ref="sessionFactory" />
		     </bean>
		 
		     <tx:advice id="txAdvice" transaction-manager="transactionManager">
		         <tx:attributes>
		             <tx:method name="*" propagation="REQUIRED" />
		         </tx:attributes>
		     </tx:advice>
		 
		     <aop:config>
		         <aop:pointcut id="interceptorPointCuts"
		             expression="execution(* com.bluesky.spring.dao.*.*(..))" />
		         <aop:advisor advice-ref="txAdvice"
		             pointcut-ref="interceptorPointCuts" />       
		     </aop:config>     
		 </beans>
		
		
		(5)注解
		
		 /**
		      * 1.添加事务注解
		      * 使用propagation 指定事务的传播行为，即当前的事务方法被另外一个事务方法调用时如何使用事务。
		      * 默认取值为REQUIRED，即使用调用方法的事务
		      * REQUIRES_NEW：使用自己的事务，调用的事务方法的事务被挂起。
		      *
		      * 2.使用isolation 指定事务的隔离级别，最常用的取值为READ_COMMITTED
		      * 3.默认情况下 Spring 的声明式事务对所有的运行时异常进行回滚，也可以通过对应的属性进行设置。通常情况下，默认值即可。
		      * 4.使用readOnly 指定事务是否为只读。 表示这个事务只读取数据但不更新数据，这样可以帮助数据库引擎优化事务。若真的是一个只读取数据库值得方法，应设置readOnly=true
		      * 5.使用timeOut 指定强制回滚之前事务可以占用的时间。
		      */
		     @Transactional(propagation=Propagation.REQUIRES_NEW,
		             isolation=Isolation.READ_COMMITTED,
		             noRollbackFor={UserAccountException.class},
		             readOnly=true, timeout=3)
		     public void purchase(String username, String isbn) {
		      	//todo 业务操作
		     }
		 }