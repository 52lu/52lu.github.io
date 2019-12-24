---
title: Java-基础篇-Lambda操作Map
date: 2019-12-14 20:27:19
tags: 
 - Java
categories:
 - 后端编程
---

# 1. Java8新加入的lambda方法

接口类型 | 新加入的方法
---|---
Map	| forEach()、replaceAll()、merge()、compute()、computeIfAbsent()、computeIfPresent()

![](https://52lu.github.io/images/map-extends.png)
> 由于继承关系，他们相应的子类也都会继承这些新方法。

# 2. forEach()
该方法签名为void forEach(BiConsumer<? super K,? super V> action)，作用是对Map中的每个映射执行action指定的操作，其中BiConsumer是一个函数接口，里面有一个待实现方法void accept(T t, U u)。BinConsumer接口名字和accept()方法名字都不重要。

## 2.1 示例:遍历输出Map
需求:假设有一个成绩单Map，遍历输出Map中的所有映射关系．

## 2.2 Java8以前的代码
```
HashMap<String,Integer> map = new HashMap<>();
map.put("张三", 62);
map.put("李四", 66);
map.put("王麻子", 89);
for(Map.Entry<String,Integer> entry : map.entrySet()){
    System.out.println(entry.getKey() + " => " + entry.getValue());
}
/**
输出:

李四 => 66
张三 => 62
王麻子 => 89
*/
```
## 2.3 匿名内部类代码
```
HashMap<String,Integer> map = new HashMap<>();
map.put("张三", 62);
map.put("李四", 66);
map.put("王麻子", 89);
map.forEach(new BiConsumer<String, Integer>() {
    @Override
    public void accept(String s, Integer integer) {
        System.out.println(s + " => " + integer);
    }
});

/**
输出:

李四 => 66
张三 => 62
王麻子 => 89
*/
```

## 2.4 使用lambda
```
HashMap<String,Integer> map = new HashMap<>();
map.put("张三", 62);
map.put("李四", 66);
map.put("王麻子", 89);
map.forEach((k,v)-> {
    System.out.println(k + " => " + v);
});
/**
输出:

李四 => 66
张三 => 62
王麻子 => 89
*/
```

# 3.replaceAll()
该方法签名为replaceAll(BiFunction<? super K,? super V,? extends V> function)，作用是对Map中的每个映射执行function指定的操作，并用function的执行结果替换原来的value，其中BiFunction是一个函数接口，里面有一个待实现方法R apply(T t, U u)

## 3.1 示例:将单词都转换成大写
需求:有一个数字到对应英文单词的Map，将单词都转换成大写．

## 3.2 Java8以前的代码
```
HashMap<Integer,String> map = new HashMap<>();
map.put(1, "c");
map.put(2, "go");
map.put(3, "java");
map.put(4, "php");
for (Map.Entry<Integer,String> entry : map.entrySet()){
   entry.setValue(entry.getValue().toUpperCase());
}
System.out.println(map);
//输出:{1=C, 2=GO, 3=JAVA, 4=PHP}
```

## 3.3 匿名内部类代码
```
HashMap<Integer,String> map = new HashMap<>();
map.put(1, "c");
map.put(2, "go");
map.put(3, "java");
map.put(4, "php");
map.replaceAll(new BiFunction<Integer, String, String>() {
    @Override
    public String apply(Integer integer, String s) {
        return s.toUpperCase();
    }
});
System.out.println(map);
// 输出:{1=C, 2=GO, 3=JAVA, 4=PHP}
```
## 3.4 使用lambda
```
HashMap<Integer,String> map = new HashMap<>();
map.put(1, "c");
map.put(2, "go");
map.put(3, "java");
map.put(4, "php");
map.replaceAll((k,v)->v.toUpperCase());
System.out.println(map);
// 输出:{1=C, 2=GO, 3=JAVA, 4=PHP}
```
# 4. merge()

该方法签名为merge(K key, V value, BiFunction<? super V,? super V,? extends V> remappingFunction)。作用是:它将新的值赋值到 key （如果不存在）或更新给定的key 值对应的 value。即key存在则更新，不存在则赋值。

## 4.1 错误信息拼接

一个比较常见的场景是将新的错误信息拼接到原来的信息上，比如：
```
HashMap<String, String> stringStringHashMap = new HashMap<>(1);
stringStringHashMap.put("error", "执行错误,");
System.out.println(stringStringHashMap); // {error=执行错误}
// 使用merge
stringStringHashMap.merge("error", "其他错误信息.....", (v1, v2) -> v1 + v2);
System.out.println(stringStringHashMap); // {error=执行错误,其他错误信息.....}
```

## 4.2 其他示例
- [Java 8 中 Map 骚操作之 merge() 的用法](https://juejin.im/post/5d9b455ae51d45782b0c1bfb)


# 5.compute()
该方法签名为compute(K key, BiFunction<? super K,? super V,? extends V> remappingFunction)，作用是把remappingFunction的计算结果关联到key上，如果计算结果为null，则在Map中删除key的映射．

## 5.1 统计一个List<String> 中每个元素出现的次数

```
List<String> list = Arrays.asList("a", "b", "b", "c", "c", "c", "d", "d", "d", "f", "f", "g");
System.out.println("使用 compute 计算元素出现的次数:");
Map<String, Integer> countsMap = new HashMap<>(6);
// 此时：新值 = 旧值 + 1
list.forEach(str -> countsMap.compute(str, (k, v) -> v == null ? 1 : v + 1));
System.out.println(countsMap);
/**
使用 compute 计算元素出现的次数:
{a=1, b=2, c=3, d=3, f=2, g=1}
*/
```

# 6.computeIfAbsent()
该方法签名为V computeIfAbsent(K key, Function<? super K,? extends V> mappingFunction)，
<font color=green>作用:只有在当前Map中不存在key值的映射或映射值为null时，才调用mappingFunction，并在mappingFunction执行结果非null时，将结果跟key关联．</font>


## 6.1 示例:条件判断和添加操作合二为一
computeIfAbsent()常用来对Map的某个key值建立初始化映射．比如我们要实现一个多值映射，Map的定义可能是Map<K,Set<V>>，要向Map中放入新值，可通过如下代码实现

## 6.2 Java8以前的代码
```
Map<Integer, Set<String>> map = new HashMap<>();
if(map.containsKey(1)){
    map.get(1).add("PHP");
}else{
    Set<String> valueSet = new HashSet<String>();
    valueSet.add("PHP");
    map.put(1, valueSet);
}
```
## 6.3 使用lambda
```
Map<Integer, Set<String>> map = new HashMap<>();
map.computeIfAbsent(1, v -> new HashSet<String>()).add("Java");
```


# 7.computeIfPresent()

该方法签名为V computeIfPresent(K key, BiFunction<? super K,? super V,? extends V> remappingFunction)，<font color=green>作用跟computeIfAbsent()相反，即，只有在当前Map中存在key值的映射且非null时，才调用remappingFunction，如果remappingFunction执行结果为null，则删除key的映射，否则使用该结果替换key原来的映射．</font>


## 7.1 使用示例
```
HashMap<String, Integer> map = new HashMap<>();
map.put("1", 1);
map.put("2", 2);
map.put("3", 3);
map.put("4", 3);
//只对map中存在的key对应的value进行操作
Integer integer = map.computeIfPresent("3", (k, v) -> v + 1);
System.out.printf("修改key=3得值为: %d\n", integer);
System.out.printf("修改后的map为: %s\n", map.toString());
// 触发remappingFunction，并返回执行结果为null，从而删除key=4
map.computeIfPresent("4", (k, v) -> {
    System.out.println("调用remappingFunction = [k=" + k + "; v=" + v + "]");
    return null;
});
System.out.printf("触发remappingFunction后的，map为: %s\n", map.toString());

/**
输出:
修改key=3得值为: 4
修改后的map为: {1=1, 2=2, 3=4, 4=3}
调用remappingFunction = [k=4; v=3]
触发remappingFunction后的，map为: {1=1, 2=2, 3=4}
*/
```
