Hibernate is an object-relational mapping (ORM) library for Java widely used in industry to provide a persistence of Java objects into relational databases.

The main purpose of Hibernate is to abstract the database operations so that developers could handle the objects without worries about the SQL calls and object conversions happening underneath the application.
In fact, Hibernate generates the SQL queries playing the role of interface between the application and the database.

An extensive documentation can be found over the web, including at Hibernate's [website](http://www.hibernate.org/), where the library itself is also available for download.
Since I'm using MySQL in this example, the respective [connector](http://dev.mysql.com/downloads/connector/j/#downloads) for Java is required.

The configuration of Hibernate is described in the `hibernate.cfg.xml`, usually located at the root of your project folder.
An example of its content is seem below.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
				"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
				"http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
	<session-factory>
		<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
		<property name="hibernate.connection.password">hibdb</property>
		<property name="hibernate.connection.url">jdbc:mysql://localhost:3306/hibdb_schema</property>
		<property name="hibernate.connection.username">hibdb</property>
		<property name="hibernate.default_schema">hibdb_schema</property>
		<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
		<property name="hibernate.current_session_context_class">org.hibernate.context.internal.ThreadLocalSessionContext</property>
		
		<!-- Related to mapping START -->
				<mapping resource="com/rezend/hibdb/Alarm.hbm.xml" />
				<!-- Related to the mapping END -->
		
	</session-factory>
</hibernate-configuration>
```

The next step consists basically on mapping the attributes of your Java class to the fields of your database.
This mapping can be done either through [*Java Persistence Annotations (JPA)*](http://docs.jboss.org/hibernate/annotations/3.5/reference/en/html_single/#entity-mapping) or by setting up an additional XML mapping file.
This example, specifically, uses the last method.
The respective mapping file should be included in the `hibernate.cfg.xml` seen above.

Let's start then by creating our class **Alarm.java**, with the attributes *id*, *description* and *instant*.

```java
public class Alarm implements java.io.Serializable {

	private Integer id;
	private String description;
	private Date instant;

	public Integer getId() {
			return this.id;
	}

	public void setId(Integer id) {
			this.id = id;
	}
	
	public String getDescription() {
			return this.description;
	}

	public void setDescription(String description) {
			this.description = description;
	}
	
	public Date getInstant() {
			return this.instant;
	}

	public void setInstant(Date instant) {
			this.instant = instant;
	}
}
```

This class is a simple [POJO](http://en.wikipedia.org/wiki/Plain_Old_Java_Object) object, with getters and setters for each attribute.

`Note: Some IDEs (i.e. Eclipse) can generate the POJO class automatically from a database specification.`

Given a table *alarms* with fields *id*, *descr* and *instant*, the respective mapping between **Alarm.java** and this table would be the following:

```xml
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<!-- Generated Jul 23, 2013 5:39:14 AM by Hibernate Tools 4.0.0 -->
<hibernate-mapping>
    <class name="com.rezend.hibdb.Alarm" table="alarms">
        <id name="id" type="java.lang.Integer">
            <column name="id" />
            <generator class="identity" />
        </id>
        <property name="description" type="string">
            <column name="descr" length="45" />
        </property>
        <property name="instant" type="timestamp">
            <column name="instant" length="19" not-null="true" />
        </property>
    </class>
</hibernate-mapping>
```

The first line within the `<hibernate-mapping>` tag relates the **Alarm.java** class with the table *alarms*.
The primary key *id* receives a special tag where `<id name="id"...>` corresponds to the Java attribute and the `<column name="id"/>` to the database field. The *id* attribute is auto-generated.

The remaining properties follow the same principle, adding few other properties, such maximum `length` and `not-null`. See more properties [here](http://docs.jboss.org/hibernate/orm/4.2/devguide/en-US/html/ch01.html#d5e347).

Finally, Hibernate uses sessions to persist the object to database.

```java
// Creating first alarm with default values
Alarm alarm = new Alarm();
alarm.setDescription("foo");
alarm.setInstant(new Date(1384124344));

// Create transaction and session
Transaction trns = null;
Session session = getSessionFactory().openSession();
try {
	trns = session.beginTransaction();
	session.save(alarm);
	session.getTransaction().commit();
} catch (RuntimeException e) {
	// If something goes wrong, rollback
	if (trns != null) {
		trns.rollback();
	}
} finally {
	session.flush();
	session.close();
}
```

This is the nearly the simplest way to setup and use Hibernate, although it is indeed a powerful tool with many other features.

The [Hibernate Community Documentation](http://docs.jboss.org/hibernate/orm/4.2/devguide/en-US/html/) is undoubtedly the best starting point.
