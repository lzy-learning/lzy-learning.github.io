# Maven: Project manager



# Configuration of Beans

## Through Annotation

Annotations in Spring generally fall into two categories: component declaration and  wiring. Component declaration means that a class is managed by a container, and wiring  means that the class has resources that are injected (or “wired”) by the container.

Firstly, the configuration file can be defined to tell Spring framework  the component-scan path, that is:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd">
       <context:component-scan base-package="com.bsg6.chapter03.mem01" />
</beans>
```

Then the components which can be used to inject are declared with the annotation `@Component`, which like the following:

```java
@Component
public class MyService implement Service{}
```

Finally, declare the class which needs to use the registered component with `@ContextConfiguration(‘path-to-config.xml’)`. The fields need to be wired in that class can be injected automatically by scanning the specified component paths to get an available object/component.

```java
@ContextConfiguration('/config.xml')
public class Test{
    @Autowired
    private ApplicationContext context;
    @Autowired
    private Service service;
}
```

Particularly, component can be chosen by annotation `@Component(‘component-name’)` and `@Qualifier("component-name")`. For example:

```java
@Component("bar")
public class MyService implement Service{}
```

```java
public class Test{
    @Autowired
    @Qualifier("bar")
    private Service service;
}
```

