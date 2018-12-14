> 案例概述

大多数JPA驱动的应用程序大量使用“persistence.xml”文件来获取JPA实现，例如[Hibernate](http://hibernate.org/)或[OpenJPA](http://openjpa.apache.org/)。

我们的方法提供了一种集中式机制，用于配置一个或多个持久性单元 和相关的持久性上下文。

虽然这种方法本身并不是错误的，但它并不适用于需要单独测试使用不同持久性单元的应用程序组件的用例。

从好的方面来说，只需使用普通的Java就可以在不使用“persistence.xml”文件的情况下引导JPA实现。

在本文中，我们将看到如何使用Hibernate完成此任务。

> 实现PersistenceUnitInfo接口

在典型的“基于xml”的JPA配置中，JPA实现自动负责实现[PersistenceUnitInfo](https://docs.oracle.com/javaee/7/api/javax/persistence/spi/PersistenceUnitInfo.html)接口。

使用通过解析“persistence.xml”文件收集的所有数据，持久性提供程序使用此实现来创建实体管理器工厂。从这个工厂，我们可以获得一个实体。

**由于我们不依赖于“persistence.xml”文件，我们需要做的第一件事就是提供我们自己的PersistenceUnitInfo实现。**我们将Hibernate用于持久性提供程序：

```java
public class HibernatePersistenceUnitInfo implements PersistenceUnitInfo {
     
    public static String JPA_VERSION = "2.1";
    private String persistenceUnitName;
    private PersistenceUnitTransactionType transactionType
      = PersistenceUnitTransactionType.RESOURCE_LOCAL;
    private List<String> managedClassNames;
    private List<String> mappingFileNames = new ArrayList<>();
    private Properties properties;
    private DataSource jtaDataSource;
    private DataSource nonjtaDataSource;
    private List<ClassTransformer> transformers = new ArrayList<>();
     
    public HibernatePersistenceUnitInfo(
      String persistenceUnitName, List<String> managedClassNames, Properties properties) {
        this.persistenceUnitName = persistenceUnitName;
        this.managedClassNames = managedClassNames;
        this.properties = properties;
    }
 
    // standard setters / getters   
}
```

简而言之，HibernatePersistenceUnitInfo类只是一个普通的数据容器，它存储绑定到特定持久性单元的配置参数。这包括持久性单元名称，管理实体类的名称，事务类型，JTA和非JTA数据源等。

> 使用Hibernate的EntityManagerFactoryBuilderImpl类创建实体管理器工厂

现在我们已经实现了自定义PersistenceUnitInfo实现，我们需要做的最后一件事就是获得一个实体管理器工厂。

Hibernate使用[EntityManagerFactoryBuilderImpl](https://docs.jboss.org/hibernate/orm/5.0/javadocs/org/hibernate/jpa/boot/internal/EntityManagerFactoryBuilderImpl.html)类（构建器模式的简洁实现）使这个过程变得轻而易举。

为了提供更高级别的抽象，让我们创建一个包含EntityManagerFactoryBuilderImpl功能的类。

首先，让我们展示使用Hibernate的EntityManagerFactoryBuilderImpl类和 HibernatePersistenceUnitInf类创建实体管理器工厂和实体管理器的方法  ：

```java
public class JpaEntityManagerFactory {
    private String DB_URL = "jdbc:mysql://databaseurl";
    private String DB_USER_NAME = "username";
    private String DB_PASSWORD = "password";
    private Class[] entityClasses;
     
    public JpaEntityManagerFactory(Class[] entityClasses) {
        this.entityClasses = entityClasses;
    }
     
    public EntityManager getEntityManager() {
        return getEntityManagerFactory().createEntityManager();
    }
     
    protected EntityManagerFactory getEntityManagerFactory() {
        PersistenceUnitInfo persistenceUnitInfo = getPersistenceUnitInfo(
          getClass().getSimpleName());
        Map<String, Object> configuration = new HashMap<>();
        return new EntityManagerFactoryBuilderImpl(
          new PersistenceUnitInfoDescriptor(persistenceUnitInfo), configuration)
          .build();
    }
     
    protected HibernatePersistenceUnitInfo getPersistenceUnitInfo(String name) {
        return new HibernatePersistenceUnitInfo(name, getEntityClassNames(), getProperties());
    }
 
    // additional methods
}
```

接下来，让我们看一下提供EntityManagerFactoryBuilderImpl和HibernatePersistenceUnitInfo所需参数的方法 。

这些参数包括托管实体类，实体类的名称，Hibernate的配置属性和MysqlDataSource对象：

```java
public class JpaEntityManagerFactory {
    //...
     
    protected List<String> getEntityClassNames() {
        return Arrays.asList(getEntities())
          .stream()
          .map(Class::getName)
          .collect(Collectors.toList());
    }
     
    protected Properties getProperties() {
        Properties properties = new Properties();
        properties.put("hibernate.dialect", "org.hibernate.dialect.MySQLDialect");
        properties.put("hibernate.id.new_generator_mappings", false);
        properties.put("hibernate.connection.datasource", getMysqlDataSource());
        return properties;
    }
     
    protected Class[] getEntities() {
        return entityClasses;
    }
     
    protected DataSource getMysqlDataSource() {
        MysqlDataSource mysqlDataSource = new MysqlDataSource();
        mysqlDataSource.setURL(DB_URL);
        mysqlDataSource.setUser(DB_USER_NAME);
        mysqlDataSource.setPassword(DB_PASSWORD);
        return mysqlDataSource;
    }
}
```

为简单起见，我们在JpaEntityManagerFactory类中对数据库连接参数进行了硬编码。但是，在生产中，我们应该将它们存储在单独的属性文件中。

此外，getMysqlDataSource()方法返回一个完全初始化的MysqlDataSource对象。

我们这样做只是为了让事情更容易理解。在一种更实际、松耦合的设计中，我们将使用EntityManagerFactoryBuilderImpl的[withDataSource()](https://docs.jboss.org/hibernate/orm/5.0/javadocs/org/hibernate/jpa/boot/internal/EntityManagerFactoryBuilderImpl.html#withDataSource-javax.sql.DataSource-)方法注入数据源对象，而不是在类中创建它。

> 使用实体管理器执行CRUD操作

最后，让我们看看如何使用JpaEntityManagerFactory实例获取JPA实体管理器并执行CRUD操作。（请注意，为简洁起见，我们省略了User类）：

```java
public static void main(String[] args) {
    EntityManager entityManager = getJpaEntityManager();
    User user = entityManager.find(User.class, 1);
     
    entityManager.getTransaction().begin();
    user.setName("John");
    user.setEmail("john@domain.com");
    entityManager.merge(user);
    entityManager.getTransaction().commit();
     
    entityManager.getTransaction().begin();
    entityManager.persist(new User("Monica", "monica@domain.com"));
    entityManager.getTransaction().commit();
  
    // additional CRUD operations
}
 
private static class EntityManagerHolder {
    private static final EntityManager ENTITY_MANAGER = new JpaEntityManagerFactory(
      new Class[]{User.class})
      .getEntityManager();
}
 
public static EntityManager getJpaEntityManager() {
    return EntityManagerHolder.ENTITY_MANAGER;
}
```

> 案例结论

在本文中，我们展示了如何使用JPA的PersistenceUnitInfo接口和Hibernate的EntityManagerFactoryBuilderImpl类的自定义实现以编程方式引导JPA实体管理器，而不必依赖传统的“persistence.xml”文件。