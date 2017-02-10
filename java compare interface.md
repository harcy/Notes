# Comparable & Comparator

- java.lang.Comparable
  
  @override
  int compareTo()
  
  Collections.sort(ObjectList)
  
- java.util.Comparator
  
  @override
  int compare()
  
  Collections.sort(ObjectList, comparatorInstance)
  
  Comparator interface使用了策略设计模式(策略模式核心引入context类，并拥有strategy的引用), 排序类中持有一个comparator接口的引用,
  类似context的角色, 传入各种自定义排序规则的Comparator实现类, 从而使用不同的算法实现不同的排序.
