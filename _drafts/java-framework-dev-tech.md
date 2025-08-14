# Prerequisite

### 1. Annotation

Annotations in Java are a form of metadata that can be used to **mark classes, methods, fields, parameters, constructors, etc.**, and they are heavily used in frameworks for things like configuration, injection, scanning, and more.

They don't directly affect the logic but serve as *indicators* for the compiler or runtime tools (like Spring) to trigger behavior.

#### ✅ Meta-Annotations

Java provides built-in annotations to describe how custom annotations behave:

| Annotation    | Purpose                                                      |
| ------------- | ------------------------------------------------------------ |
| `@Retention`  | Controls **when** the annotation is available (SOURCE, CLASS, RUNTIME) |
| `@Target`     | Specifies **where** the annotation can be applied (e.g., method, field, class) |
| `@Inherited`  | Allows annotation to be **inherited by subclasses**          |
| `@Documented` | Marks annotation to be included in the **Javadoc**           |

##### Example:

```
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Service {
    String value() default "";
}
```

- `RetentionPolicy.RUNTIME` → required to access via Reflection
- `ElementType.TYPE` → can only annotate classes or interfaces

------

#### 🔍 Reading Annotations via Reflection

You can use the `Class`, `Field`, `Method`, and `Constructor` APIs to access annotations at runtime.

##### Example:

```
@Service("myService")
public class UserService {}

...

Class<?> clazz = UserService.class;
if (clazz.isAnnotationPresent(Service.class)) {
    Service serviceAnnotation = clazz.getAnnotation(Service.class);
    String name = serviceAnnotation.value(); // "myService"
}
```

You can also get **all annotations**:

```
Annotation[] annotations = clazz.getAnnotations();
```

Or for fields/methods:

```
Field field = clazz.getDeclaredField("myField");
if (field.isAnnotationPresent(Autowired.class)) {
    Autowired autowired = field.getAnnotation(Autowired.class);
}
```

------

#### 🧰 Practical Use in Frameworks

In frameworks like Spring or your own:

1. **Scan packages for classes**
2. Use `Class.forName(...)` and `Class.getAnnotations()` to find classes marked with custom annotations
3. Based on annotation metadata (like value fields), **register or instantiate beans**, **inject dependencies**, or **apply AOP logic**

------

#### 🛠️ Custom Annotation with Parameters

```
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
public @interface Loggable {
    String level() default "INFO";
}
```

This can later be read by an interceptor or AOP proxy to add custom logging logic depending on the level.

------

### ⚠️ Notes:

- Annotations can **only be accessed at runtime** if they are annotated with `@Retention(RetentionPolicy.RUNTIME)`
- You can use **Reflection** to read them, but not modify them
- Used in combination with **proxies** and **reflection**, annotations become the core mechanism for declarative programming in modern frameworks



## 2. Reflection

Reflection is Java’s powerful runtime introspection and manipulation mechanism. It allows a program to examine and modify the runtime behavior of classes, objects, fields, methods, and constructors — even private ones.

This is critical for building frameworks, especially for tasks like **dependency injection**, **annotation scanning**, and **runtime configuration**.

------

### 🔍 Key Classes in Reflection API

| Class              | Purpose                                                      |
| ------------------ | ------------------------------------------------------------ |
| `Class<?>`         | Entry point to metadata of a class                           |
| `Method`           | Represents a method                                          |
| `Field`            | Represents a class field (attribute)                         |
| `Constructor`      | Represents a constructor                                     |
| `AccessibleObject` | Superclass for `Field`, `Method`, `Constructor` — allows bypassing access control |

------

### ✅ Demo 1: Inspecting Class Metadata

```
public class User {
    private String name;

    public User(String name) {
        this.name = name;
    }

    public void greet() {
        System.out.println("Hello, " + name);
    }
}
Class<?> clazz = User.class;

System.out.println("Class Name: " + clazz.getName());
for (Field field : clazz.getDeclaredFields()) {
    System.out.println("Field: " + field.getName());
}
for (Method method : clazz.getDeclaredMethods()) {
    System.out.println("Method: " + method.getName());
}
```

------

### ✅ Demo 2: Creating and Manipulating an Instance Dynamically

```
Constructor<?> constructor = clazz.getConstructor(String.class);
Object user = constructor.newInstance("Alice");

Method greetMethod = clazz.getMethod("greet");
greetMethod.invoke(user); // prints: Hello, Alice
```

------

### ✅ Demo 3: Accessing Private Fields

```
Field nameField = clazz.getDeclaredField("name");
nameField.setAccessible(true); // Bypass private modifier
String name = (String) nameField.get(user);
System.out.println("Name: " + name);
```

------

### ⚠️ Limitations

- You **cannot change class structure** (e.g., add fields/methods) at runtime
- Performance overhead due to security checks and metadata access
- Not type-safe; requires careful exception handling



# 3. Proxy

The `java.lang.reflect.Proxy` API allows dynamic creation of proxy objects at runtime for interfaces. It is fundamental to **AOP**, **RPC**, and many DI containers (like Spring) where behaviors are injected without modifying source code.

------

### 💡 How It Works

1. You define an `InvocationHandler` that intercepts method calls
2. Use `Proxy.newProxyInstance(...)` to generate a proxy object that:
   - Implements the desired interface
   - Delegates method calls to your handler

> What `Proxy.newProxyInstance(...)` do is:
>
> 1. It creates a new class at runtime that
>    - Implements a given set of interfaces, so that you can call the APIs, which is defined in those interfaces, through the returned object of `newProxyInstance`.
>    - Forwards all method calls to an implementation of `InvocationHandler`, which implements `invoke` method.
> 2. The proxy class is generated dynamically, compiled to bytecode in memory, loaded via a custom class loader, and instantiated.

------

### ✅ Demo: Logging Proxy for a Service

```
public interface UserService {
    void saveUser(String name);
}

public class UserServiceImpl implements UserService {
    public void saveUser(String name) {
        System.out.println("User " + name + " saved!");
    }
}
public class LoggingHandler implements InvocationHandler {
    private final Object target;

    public LoggingHandler(Object target) {
        this.target = target;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before method: " + method.getName());
        Object result = method.invoke(target, args);
        System.out.println("After method: " + method.getName());
        return result;
    }
}
UserService target = new UserServiceImpl();
UserService proxy = (UserService) Proxy.newProxyInstance(
    UserService.class.getClassLoader(),
    new Class<?>[]{UserService.class},
    new LoggingHandler(target)
);

proxy.saveUser("Bob");
```

**Output:**

```
Before method: saveUser
User Bob saved!
After method: saveUser
```

------

### ⚠️ Notes

- Only works with **interfaces** (use **CGLIB** for classes)
- Methods are dispatched to the handler’s `invoke(...)` method
- Proxy class is dynamically generated at runtime (e.g., `$Proxy1`)



## 4. AOP (Aspect-Oriented Programming)

AOP is a programming paradigm that separates **cross-cutting concerns** (like logging, security, and transactions) from the core business logic.

In Java, AOP is usually implemented via **proxies** that intercept method calls and apply additional logic before, after, or around the target method.

------

### 🔍 Core Concepts

| Term           | Meaning                                                  |
| -------------- | -------------------------------------------------------- |
| **Advice**     | Code to run at a certain join point (before/after)       |
| **Pointcut**   | Predicate to match methods (e.g., annotated with `@Log`) |
| **Join Point** | A method execution or other interceptable event          |
| **Aspect**     | A modular unit containing pointcut + advice              |

------

### ✅ Demo: Manual AOP via Proxy

We’ll reuse the same proxy approach to simulate an AOP aspect:

```
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface LogExecution {}
public class AopHandler implements InvocationHandler {
    private final Object target;

    public AopHandler(Object target) {
        this.target = target;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        if (method.isAnnotationPresent(LogExecution.class)) {
            System.out.println("===> Logging before: " + method.getName());
        }
        return method.invoke(target, args);
    }
}
public class OrderService {
    @LogExecution
    public void placeOrder() {
        System.out.println("Order placed.");
    }
}
```

You’ll need to use CGLIB here since `OrderService` is a class, not an interface — but the idea is the same: intercept and conditionally execute code based on annotations.

------

### 🔧 In Spring AOP

Spring automatically:

- Detects classes/methods using annotations (e.g., `@Transactional`, `@Async`)
- Creates proxies using JDK (interface) or CGLIB (class)
- Routes method calls through AOP advice handlers



# A Typical Project

https://github.com/Patresss/Java-Own-Framework---step-by-step.git