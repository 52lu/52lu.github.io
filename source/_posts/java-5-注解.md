---
title: 学Java笔记-注解
date: 2019-10-15 19:19:48
tags:
 - Java
categories:
 - 后端编程
---

# 1.什么是注解？
标注（Annotations）就是代码里的标记，它提供与程序有关的数据，但是标注本身不是程序一部分。标注不对其所注解的代码的操作有直接的影响。在有的书中，将标注也称为注解。通过使用标注，程序开发人员可以在不改变原有逻辑的情况下，在源文件中嵌入一些补充的信息。代码分析工具、开发工具和部署工具可以通过这些补充信息进行验证或进行部署。

# 2.注解的作用
简单地说，标注就是代码里的标记，这些标记在类加载、运行时或编译的时候可以被解释，但是不对程序的运行产生直接的影响。标注有很多作用，包括：
- 为编译器提供信息。编译器可以使用标注来检测错误或禁止警告。

- 在编译和部署时处理。软件开发工具可以处理标注信息以生成代码、XML文件等。
- 在运行时处理。有些标注在运行时可以被检查并使用。

>从某些方面看，标注就像修饰符一样被使用，<font color=red>并应用于包、类型、构造方法、方法、成员变量、参数、本地变量的声明中。</font>标注可以应用到程序的类的声明、字段的声明、方法的声明及其他程序元素的声明中。按惯例标注要出现在它自己所在行的第一行，并且可以包括带有名称或未命名值的元素，

# 3.注解的分类
Java的注解可以分为三类：

- 第一类是由编译器使用的注解，

例如：

```
@Override：让编译器检查该方法是否正确地实现了重写；
@SuppressWarnings：告诉编译器忽略此处代码产生的警告。
@Deprecated: 被此注解标记的类或者方法表示不再推荐使用此类或者方法
```
**这类注解不会被编译进入.class文件，它们在编译后就被编译器扔掉了。**

- 第二类是由工具处理.class文件使用的注解

比如有些工具会在加载class的时候，对class做动态修改，实现一些特殊的功能。这类注解会被编译进入.class文件，但加载结束后并不会存在于内存中。这类注解只被一些底层库使用，一般我们不必自己处理。


- 第三类是在程序运行期能够读取的注解，它们在加载后一直存在于JVM中，这也是最常用的注解。

# 4.内置注解
## 4.1 @Override
@Override 是一个标记注解，表示重写父类方法。
**使用实例:**

```
// UserDao 接口
public interface IUserDao {
	User loadUserByUsername(String username);

	// ...
}

// UserDao 实现类
public class UserDao implement IUserDao {

	@Override
	public User loadUserByUsername(String username) {
		// TODO: 
	}

	// ...
}
```
## 4.2 @SuppressWarnnings
@SuppressWarnnings 抑制编译器警告，取值为:
- deprecation ：使用了不赞成使用的类或方法时的警告；
- unchecked ：执行了未检查的转换时的警告，例如当使用集合时没有用泛型 (Generics) 来指定集合保存的类型;
- fallthrough ：当 Switch 程序块直接通往下一种情况而没有 Break 时的警告;
- path ：在类路径、源文件路径等中有不存在的路径时的警告;
- serial ：当在可序列化的类上缺少 serialVersionUID 定义时的警告;
- finally ：任何 finally 子句不能正常完成时的警告;
- all ：关于以上所有情况的警告。

**实例**
```
public class UserDao {

    @SuppressWarnings(value = {"rawtypes", "unchecked"})
    public List<User> getUserList() {
        List<User> list = new ArrayList();
        return list;
    }

    // ...
}
```
## 4.3 @Deprecated
被此注解标记的类或者方法表示不再推荐使用此类或者方法。 此外对应的 java doc 中有一个 @deprecated tag。

**实例**
```
public class UserDao {

	/**
	 * 根据用户的登录昵称拉取用户信息
	 *
	 * @param username 登录昵称
	 * @return 用户信息
	 */
	public User loadUserByUsername(String username) {
		// ...
	}


	/**
	 * 根据用户的真实姓名拉取用户信息
	 *
	 * @param realname 真实姓名
	 * @return 用户信息
	 * @deprecated 该方法已过期，不推荐使用
	 */
	@Deprecated
	public User loadUserByRealname(String realname) {
		// ...
	}

	// ...
}
```

# 5.元注解
元注解的作用就是负责注解其他注解.

**Java5.0定义的元注解：**
## 5.1 @Target:
指定注解所修饰对象的范围。
- ElementType.CONSTRUCTOR :用于描述构造器
- ElementType.FIELD :用于描述域
- ElementType.LOCAL_VARIABLE :用于描述局部变量
- ElementType.METHOD :用于描述方法
- ElementType.PACKAGE :用于描述包
- ElementType.PARAMETER :用于描述参数
- ElementType.TYPE :用于描述类、接口(包括注解类型) 或enum声明

## 5.2 @Retention
定义Annotation保留的时间长短。

- RetentionPoicy.SOURCE :在源文件中有效（即源文件保留）
- RetentionPoicy.CLASS :在class文件中有效（即class保留）
- RetentionPoicy.RUNTIME :在运行时有效（即运行时保留），在运行时保留可以通过反射获取到该值的属性值同时相应做一些逻辑处理

## 5.3 @Documented
标记注解，含有该注解类型的元素(带有注释的)会通过javadoc或类似工具进行文档化。
## 5.4 @Inherited
表示注解类型能被自动继承。 如果一个类使用了 @Inherited 类型的注解，则此类的子类也将含有该注解，当 Retention 为 RUNTIME 时通过反射API可以查询到此类以及其父类直到发现指定的annotation类型被发现，或者到达类继承结构的顶层。


# 6.自定义注解
注解类型的定义有点类似接口的定义，使用<font color=red>@interface关键字用来声明一个注解，</font>实际上标注类型是接口的一种。里面的每一个方法表示声明了一个可配置的参数，方法名即为参数名。

- 访问修饰符只能使用 public 或者 default（默认的、友好的）
- 返回值类型就是参数的类型且只能为 八种基本数据类型 、 Class 、 String 、 Enum 、Annotations类型以及上述类型所组成的 数组
- 通过 default 关键字声明参数的默认值。
- 如果只有一个参数成员最好命名为 value()
- 注解元素必须有确定的值，要么在定义注解元素时默认值指定，要么使用此注解时指定。非基本类型注解元素的值不可为 null


## 6.1 第1步:用@interface定义注解
```
public @interface Report {
}
```
## 6.2 第2步:添加参数、默认值
```
public @interface Report {
    int type() default 0;
    String level() default "info";
    String value() default "";
    ...;
}
```
## 6.3 用元注解配置注解：
```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface Report {
    int type() default 0;
    String level() default "info";
    String value() default "";
    ...;
}
```
> 其中，必须设置@Target和@Retention，@Retention一般设置为RUNTIME，因为我们自定义的注解通常要求在运行期读取。一般情况下，不必写@Inherited和@Repeatable。

# 7. 解析注解
Java的注解本身对代码逻辑没有任何影响。根据@Retention的配置：

- SOURCE类型的注解在编译期就被丢掉了；
- CLASS类型的注解仅保存在class文件中，它们不会被加载进JVM；
- RUNTIME类型的注解会被加载进JVM，并且在运行期可以被程序读取。

如何使用注解完全由工具决定。SOURCE类型的注解主要由编译器使用，因此我们一般只使用，不编写。CLASS类型的注解主要由底层工具库使用，涉及到class的加载，一般我们很少用到。只有RUNTIME类型的注解不但要使用，还经常需要编写。

因此，我们只讨论如何读取RUNTIME类型的注解。

## 7.1 读取注解
因为注解定义后也是一种class，所有的注解都继承自java.lang.annotation.Annotation，因此，读取注解，需要使用反射API。Java提供的使用反射API读取Annotation的方法包括：

### 7.1.1 判断某个注解是否存在于Class、Field、Method或Constructor：
**方法列表:**

- Class.isAnnotationPresent(Class) 
- Field.isAnnotationPresent(Class)
- Method.isAnnotationPresent(Class)
- Constructor.isAnnotationPresent(Class)

**实例**
```
// 判断@Report是否存在于Person类:
Person.class.isAnnotationPresent(Report.class);
```

### 7.1.2 直接读取注解的方法列表
- Class.getAnnotation(Class)
- Field.getAnnotation(Class)
- Method.getAnnotation(Class)
- Constructor.getAnnotation(Class)

**实例**
```
// 获取Person定义的@Report注解:
Report report = Person.class.getAnnotation(Report.class);
int type = report.type();
String level = report.level();
```


### 7.1.3 射API读取注解的两种方法

- 是先判断Annotation是否存在，如果存在，就直接读取

```
Class cls = Person.class;
if (cls.isAnnotationPresent(Report.class)) {
    Report report = cls.getAnnotation(Report.class);
    ...
}
```

- 直接读取Annotation，如果Annotation不存在，将返回null

```
Class cls = Person.class;
Report report = cls.getAnnotation(Report.class);
if (report != null) {
   ...
}
```

# 8.第三方注解 
## 8.1 springmvc校验注解
- @NotNull: 注解元素必须是非空
- @Null: 注解元素必须是空
- @Digits: 验证数字构成是否合法
- @Future: 验证是否在当前系统时间之后
- @Past: 验证是否在当前系统时间之前
- @Max: 验证值是否小于等于最大指定整数值
- @Min: 验证值是否大于等于最小指定整数值
- @Pattern: 验证字符串是否匹配指定的正则表达式
- @Size: 验证元素大小是否在指定范围内
- @DecimalMax: 验证值是否小于等于最大指定小数值
- @DecimalMin: 验证值是否大于等于最小指定小数值
- @AssertTrue: 被注释的元素必须为true
- @AssertFalse: 被注释的元素必须为false
- @Email: 被注释的元素必须是电子邮箱地址
- @Length: 被注释的字符串的大小必须在指定的范围内
- @NotEmpty: 被注释的字符串的必须非空
- @Range: 被注释的元素必须在合适的范围内