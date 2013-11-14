---
layout: post
title: Data Access Object (DAO) for Hibernate
---

The [last post](/persistence-with-hibernate) introduced a basic implementation using Hibernate.
At the end, few lines presented a secure way to store data into the database, with the ability of undoing the changes in case of failure *(rollback)*.

Usually, read and write operations are extensively used along the application.
A good practice, however, is that its business logic should be decoupled from the data storage to guarantee a uniform and reliable data persistence.
In other words, the business logic of the application should not be concerned about how and where the information is stored.

This intermediate entity is usually a [*Data Access Object (DAO)*](http://en.wikipedia.org/wiki/Data_access_object), that provides simple *CRUD (create, read, update, delete)* methods to the upper layer, and handle the transactions with the database, the bottommost layer.

The respective DAO for the previous post using Hibernate can be implemented as follows:

```java
/**
 * Alarm Data Access Object. Contains a session factory builder and methods to
 * perform transactions with the database.
 */
public class AlarmDAO {

	/**
	 * Global session factory. It is a thread-safe unique factory for the whole
	 * application.
	 */
	private static final SessionFactory sessionFactory = buildSessionFactory();

	/**
	 * Section factory builder.
	 * @return a session factory.
	 */
	private static SessionFactory buildSessionFactory() {
		try {
			// Create the SessionFactory from hibernate.cfg.xml
			return new Configuration().configure().buildSessionFactory();
		} catch (Throwable ex) {
			// Make sure you log the exception, as it might be swallowed
			System.err.println("Initial SessionFactory creation failed." + ex);
			throw new ExceptionInInitializerError(ex);
		}
	}

	/**
	 * Getter of global session factory.
	 * @return global session factory.
	 */
	public static SessionFactory getSessionFactory() {
		return sessionFactory;
	}
```

Every DAO instantiate a `SessionFactory` once.
The method `getSessionFactory()` is then invoked every time a new transaction has to be performed.
Following it is shown the implementations of methods `addAlarm(...)`, `updateAlarm(...)` and `deleteAlarm(...)`.

```java
/**
 * Add alarm to the database. The alarm parameter receives a new ID (key)
 * even if it had a previously assigned ID.
 * 
 * @param alarm Alarm to be added.
 */
public void addAlarm(Alarm alarm) {

	Transaction trns = null;
	Session session = getSessionFactory().openSession();
	try {
		trns = session.beginTransaction();
		session.save(alarm);
		session.getTransaction().commit();
	} catch (RuntimeException e) {
		if (trns != null) {
			trns.rollback();
		}
	} finally {
		session.flush();
		session.close();
	}
}

/**
 * Update alarm in the database. The key is the alarm ID.
 * 
 * @param alarm Alarm to be updated.
 */
public void updateAlarm(Alarm alarm) {
	Transaction trns = null;
	Session session = getSessionFactory().openSession();
	try {
		trns = session.beginTransaction();
		session.update(alarm);
		session.getTransaction().commit();
	} catch (RuntimeException e) {
		if (trns != null) {
			trns.rollback();
		}
	} finally {
		session.close();
	}
}

/**
 * Delete the alarm passed as parameter. The key is the alarm ID. The ID
 * must exist in the database. Otherwise the function generates an
 * exception.
 * 
 * @param alarm Alarm to be deleted.
 */
public void deleteAlarm(Alarm alarm) {
	Transaction trns = null;
	Session session = getSessionFactory().openSession();
	try {
		trns = session.beginTransaction();
		session.delete(alarm);
		session.getTransaction().commit();
	} catch (RuntimeException e) {
		if (trns != null) {
			trns.rollback();
		}
	} finally {
		session.flush();
		session.close();
	}
}
```

In every method above, a `Transaction` is started for a session.
If something unexpected happens while the write operation fails, the transaction is undone and the session closed.
The `flush()` method forces Hibernate to synchronize the in-memory state of the session with the database.

Through this DAO, the higher layers of the application are then able to persist information into the database in a transparent way:

```java
// Creating DAO to handle DB transactions
AlarmDAO alarmDAO = new AlarmDAO();

// Creating first alarm with default values
Alarm alarm1 = new Alarm();

// Adding alarm with default values
alarmDAO.addAlarm(alarm1);

// After added, alarm1 has received the auto-incremental ID
System.out.println("ID of alarm1: " + alarm1.getId());

// Adding a new description to the default alarm and updating in the
// database. The reference is the ID of the alarm.
alarm1.setDescription("yada yada yada");
alarmDAO.updateAlarm(alarm1);

// So, to delete the just inserted alarm
alarmDAO.deleteAlarm(alarm2);
```

As a result, the business logic can change and invoke the DAO methods in different contexts without need to change the DAO itself.
Similarly, any modification related to the ORM implementation (Hibernate in this case) will be reflected up to the DAO, so that the usage by the business logic remains untouched as long as the signature of the DAO methods are kept.

The complete code is available on my [repository](https://github.com/rafaelrezend/HibernateSandbox).
