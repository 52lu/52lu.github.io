---
title: 学Java笔记-枚举
date: 2019-10-15 19:20:56
 - Java
categories:
 - 后端
---
# 1.枚举类型
在java语言中，使用关键字enum定义一个枚举类型

**注意事项**
- 所有的枚举都隐含地继承自 java.lang.Enum,因为Java不支持多重继承，所以枚举类型不能再继承其他类
- 枚举类型的构造器必须为包级私有或私有的
- 枚举之间是逗号，与其他语法的间隔是分号。

## 1.1 无参构造器的枚举
**声明**
```
public enum 类名 {
   ...
}
```
**实例**
```
public enum EnumStudy {
    A, B, C, D;
    // ....
}
```
## 1.2 有参构造器的枚举
**声明**
```
public enum 类名 {
   ...
}
```
**实例**
```
public enum EnumStudy {
    A("优秀","90分以上"),
    B("优良","80~90分"),
    C("良","70~80分"),
    D("及格","60 ~70分"),
    E("不及格","60分以下");
    //自定义成员变量
    public String value;
    //自定义成员变量
    public String desc;
    EnumStudy(String value, String desc){
        this.value = value;
        this.desc = desc;
    }
}
```
## 1.3 枚举遍历
### 1.3.1 无参构造器遍历
```
// 无参构造器枚举遍历
for (EnumStudy item:EnumStudy.values()){
    System.out.println(item);
}

/**输出:
A
B
C
D
*/
```

### 1.3.2 有参构造器遍历
```
// 有参构造器枚举遍历
for (EnumStudy item:EnumStudy.values()){
    System.out.println("获得:"+item.value + ", 分值:"+item.desc);
}
/**输出:
    获得:优秀, 分值:90分以上
    获得:优良, 分值:80~90分
    获得:良, 分值:70~80分
    获得:及格, 分值:60 ~70分
    获得:不及格, 分值:60分以下
*/
```