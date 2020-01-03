---
title: jdk8 stream 基本使用（持续更新）
date: 2019-03-16 11:43:06
tags: 
	- jdk

---

**目录 (Table of Contents)**

* [过滤技巧](#过滤技巧)
* [根据某一字段对数据进行分组](#根据某一字段对数据进行分组)
* [根据某一字段计算](#根据某一字段计算)
* [获得属性内某一字段的结果集](#获得属性内某一字段的结果集)

# 过滤技巧

```java
List<Student> list = new ArrayList<>();
// 获得性别为男的学生数量
int num = list.stream()
              .filter(c>c.getSex().equals("男"))
              // 去重
              .distinct()
              .collect(Collectors.toList())
              .size();

list.filter(c>c.getSex().equals("男"))
    .forEach(x->{
    // 只循环所有性别为男的
    });
```

# 根据某一字段对数据进行分组

```java
List<Student> list = new ArrayList<>();
Map<String, List<Student>> map = list.steam()
    .collect(Collectors.groupingBy(t->t.getSex()));

```

# 根据某一字段计算

```java
List<Student> list = new ArrayList<>();
Double average= list.steam()
    .mapToDouble(Student::getAge)
    .average()
    .getAsDouble();

Double sum= list.steam()
    .mapToDouble(Student::getAge)
    .average()
    .getAsDouble();
```

# 获得属性内某一字段的结果集

```java
List<Student> list = new ArrayList<>();
List<String> sex = list.steam()
    .map(Student::getSex)
    .collect(Collectors.toList());
```

