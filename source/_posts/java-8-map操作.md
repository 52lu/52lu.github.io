---
title: 基础篇-map操作
date: 2019-10-28 11:48:54
tags:
 - Java
categories:
 - 后端编程
---
# 1.声明及初始化Map
## 1.1 先定义,后赋值
```
Map<String , Object> map = new HashMap<String , Object>();
map.put("key1", "value1");
map.put("key2", "value2");
map.put("keyN", "valueN");
```
## 1.2 双括号初始化法
```
Map<String , String> map = new HashMap<String , Object>(){{
    put("key1", "value1");
    put("key2", "value2");
    put("keyN", "valueN");
}};
```
## 1.3 使用初始块
```
public class MyMap {
     Map<String,String> maps;
    // 使用初始块定义
    {
        maps = new HashMap<>();
        maps.put("key1","value1");
        maps.put("key2","value2");
        maps.put("key3","value3");
    }

    public void printMap(){
        System.out.println(maps);
        // 输出:{key1=value1, key2=value2, key3=value3}
    }
}
```

# 2.遍历Map
## 2.1 key=>value
```
Map<Integer, Integer> map = new HashMap<Integer, Integer>(); 
for (Map.Entry<Integer, Integer> entry : map.entrySet()) { 
  System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue()); 
}
```
## 2.2 遍历keys或values
```
Map<Integer, Integer> map = new HashMap<Integer, Integer>(); 
//遍历map中的键 
for (Integer key : map.keySet()) { 
  System.out.println("Key = " + key); 
} 
//遍历map中的值 
for (Integer value : map.values()) { 
  System.out.println("Value = " + value); 
}
```
## 2.3 使用Iterator遍历
### 2.3.1 使用泛型
```

Map<Integer, Integer> map = new HashMap<Integer, Integer>(); 
Iterator<Map.Entry<Integer, Integer>> entries = map.entrySet().iterator(); 
while (entries.hasNext()) { 
  Map.Entry<Integer, Integer> entry = entries.next(); 
  System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue()); 
}


// 在keySet上

Iterator<String> iterator = map.keySet().iterator();
while (iterator.hasNext()) {
    String key = iterator.next();
    System.out.println("MyClass.testMap key=" + key);
}


//在values上

Iterator<Integer> iterator2 = map.values().iterator();
while (iterator2.hasNext()){
    Integer values = iterator2.next();
    System.out.println("MyClass.testMap values="+values);
}

```
### 2.3.2 不使用泛型
```
Map map = new HashMap(); 
Iterator entries = map.entrySet().iterator(); 
while (entries.hasNext()) { 
  Map.Entry entry = (Map.Entry) entries.next(); 
  Integer key = (Integer)entry.getKey(); 
  Integer value = (Integer)entry.getValue(); 
  System.out.println("Key = " + key + ", Value = " + value); 
}

```

## 2.4 通过键找值遍历（效率低）
```
Map<Integer, Integer> map = new HashMap<Integer, Integer>(); 
for (Integer key : map.keySet()) { 
  Integer value = map.get(key); 
  System.out.println("Key = " + key + ", Value = " + value);
}
```
# 3.map接口方法使用
## 3.1 void clear():删除该Map对象中的所有key-value对
`clear():删除该Map对象中的所有key-value对`

```
Map<String,String>  maps = new HashMap<>();
maps.put("key1","value1");
maps.put("key2","value2");
System.out.println("clear前:" + maps); 
maps.clear();
System.out.println("clear后:" + maps);

/**
* 输出:
 clear前:{key1=value1, key2=value2}
 clear后:{}
*/

```

## 3.2 boolean containsKey(Object key)
`containsKey(Object key):查询Map中是否包含指定的key，如果包含则返回true`

```
Map<String,String>  maps = new HashMap<>();
maps.put("key1","value1");
maps.put("key2","value2");
System.out.println("判断key1是否存在:" + maps.containsKey("key1"));
System.out.println("判断key8是否存在:" + maps.containsKey("key8"));
/**
* 输出:
判断key1是否存在:true
判断key8是否存在:false
*/
```

## 3.3 boolean containsValue(Object value)
`查询Map中是否包含一个或多个value，如果包含则返回true。`

```
Map<String,String>  maps = new HashMap<>();
maps.put("key1","value1");
maps.put("key2","value2");
System.out.println("判断value1是否存在:" + maps.containsValue("value1"));
System.out.println("判断value8是否存在:" + maps.containsValue("value8"));

/**
* 输出:
判断value1是否存在:true
判断value8是否存在:false
*/
```

## 3.4 Set entrySet()
` Set entrySet():返回Map中包含的key-value对所组成的Set集合，每个集合元素都是Map.Entry （Entry是Map的内部类）对象。`

```
Map<Integer, Integer> map = new HashMap<Integer, Integer>(); 
for (Map.Entry<Integer, Integer> entry : map.entrySet()) { 
  System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue()); 
}
```

## 3.5  Object get(Object key)
`返回指定key所对应的value；如果此Map中不包含该key，则返回null。`

```
Map<String,String>  maps = new HashMap<>();
maps.put("key1","value1");
maps.put("key2","value2");
System.out.println("key1的值:" + maps.get("key1"));
System.out.println("key2的值:" + maps.get("key2"));

/**
* 输出:
key1的值:value1
key2的值:value2
*/
```

## 3.6 boolean isEmpty()
`查询该Map是否为空（即不包含任何key-value对），如果为空则返回true。`

```
Map<String,String>  map1 = new HashMap<>();
map1.put("key1","value1");
map1.put("key2","value2");
Map<String,String>  map2 = new HashMap<>();
System.out.println("map1是否为空:" + map1.isEmpty());
System.out.println("map2是否为空:" + map2.isEmpty());

/**
* 输出:
map1是否为空:false
map2是否为空:true
*/

```

## 3.7 Set keySet()
`返回该Map中所有key组成的Set集合。`

```
Map<String,String>  map1 = new HashMap<>();
map1.put("key1","value1");
map1.put("key2","value2");
System.out.println("keySet结果:" + map1.keySet());

//  输出:keySet结果:[key1, key2]
```

## 3.8 Object put(Object key, Object value):
`添加一个key-value对，如果当前Map中已有一个与该key相等的key-value对，则新的key-value对会覆盖原来的key-value对。`

```
Map<String,String>  map1 = new HashMap<>();
map1.put("key1","value1");
map1.put("key1","value11");
System.out.println("keySet结果:" + map1.keySet());

//输出:map1结果:{key1=value11, key2=value2}
```

## 3.9 void putAll(Map m)
`将指定Map中的key-value对复制到本Map中。`
```
Map<String,String>  map1 = new HashMap<>();
map1.put("key1","value1");
map1.put("key2","value2");
Map<String,String>  map2 = new HashMap<>();
map2.put("a","php");
map2.put("key2","java");
map1.putAll(map2);
System.out.println("map1结果:" + map1);

//输出:{key1=value1, key2=java, a=php}
```


## 3.10 Object remove(Object key)
`删除指定key所对应的key-value对，返回被删除key所关联的value，如果该key不存在，则返回null。`
```
Map<String,String>  map1 = new HashMap<>();
map1.put("key1","value1");
map1.put("key2","value2");
map1.put("key3","value3");
map1.remove("key2");
System.out.println("map1结果:" + map1);

//输出:map1结果:{key1=value1, key3=value3}
```




## 3.11 int size()
`返回该Map里的key-value对的个数。`

```
Map<String,String>  map1 = new HashMap<>();
map1.put("key1","value1");
map1.put("key2","value2");
map1.put("key3","value3");
System.out.println("map1的key-value对的个数:" + map1.size());

//输出:map1的key-value对的个数:3
```





## 3.12 Collection values()
`返回该Map里所有value组成的Collection。`

```
Map<String,String>  map1 = new HashMap<>();
map1.put("key1","value1");
map1.put("key2","value2");
map1.put("key3","value3");
System.out.println("map1的value集合:" + map1.values());
//输出:map1的value集合:[value1, value2, value3]
```

