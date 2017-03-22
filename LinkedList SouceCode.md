### LinkedList底层使用双向链表实现
### 内部类ListIte，使用new ListItr(index),默认index=0
```java
public class ArrayList<E> extends List<E>, RandomAccess,
Cloneable, java.io.Serializable
```

### 主要方法
1. 构造方法
```java
public LinkedList(){
    
}

public LinkedList(Collection<? extends E> c){
    this();
    addAll(c); // ---下文分析
}
```

2. add类相关方法，add(E), add(int,E) ...
```java
public boolean add(E e){
    linkLast(e);
    return true;
}

private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
    
    Node(Node<E> prev, E element, Node<E> next){
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}

// Links e as last element
void linkLast(E e){
    //last, first是LinkedList的成员变量，初始化的时候都为null,分别用来指向第一个节点和最后一个节点
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if(l == null)
      first = newNode;
    else
      l.next = ndeNode;
    size++；
    modCount++;
}

public void add(int index, E element){
    checkPositionIndex(index);
    if(index == size)
      linkLast(element);
    else
      //node(index)方法找到具体的节点,在这个节点之前进行插入操作，主要考虑边界情况，比如prev节点为奴
      linkBefore(element, node(index));
}

Node<E> node(int index){
    //根据节点的个数和index值，尽量减少遍历的次数
    if (index < (size >> 1)) {
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
            return x;
    } else {
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
            return x;
    }
}
```
3. Deque接口的方法,addFirst(E),addLast(E)
```java
public void addFirst(E e){
    linkFirst(e);
}

public void addLast(E e){
    linLast(e);
}

//内部类，逆向的遍历list的节点
private class DescendingIterator implements Iterator<E>{
    private final ListItr itr = new ListItr(size());
    public boolean hasNext(){
        return itr.hasPrevious();
    }
    public E next() {
        return itr.previous();
    }
    public void remove() {
        itr.remove();
    }
}
```
4. 一些get相关的操作get(int),getFirst(),getLast()
```java
public E element(){
    return getFirst();
}

public E getFirst(){
    final Node<E> f = first;
    if(f = null)
      throw new NoSuchElementException();
    return f.item;
}
```
5. isElementIndex(int), isPositionIndex(int)
```java
//两者的区别是index < size 和 index <= size,获取element的index必须小于size,但插入element的index是可以等于size的
private boolean isElementIndex(int index) {
    return index >= 0 && index < size;
}

private boolean isPositionIndex(int index) {
    return index >= 0 && index <= size;
}
```
6. 实现Deque接口相关方法peek(),peekFirst(),peekLast(),poll(),pollFirst(),pollLast(),pop(),push(E)
```java
/**
*deque([deck])double ended queue,双端队列 
*为什么LinkedList实现Deque接口，Deque接口继承自Queue
*接口,Deque实现从顶部插入新的数据，ArrayList效率会
*非常差，因为ArrayList本身就是一个Array,而LinkedList
*可以很简单的实现这些方法
*/
//遍历获得element, Retrieves, but does not remove;
public E peek(){
    final Node<E> f = first;
    return (f == null) ? null : f.item;
}

//Retrieves and remove the head of the list
public E poll(){
    final Node<E> f = first;
    return (f == null) ? null :unlinkFirst(f);
}

private unlinkFirst(Node<E> f){
    final E element = f.item;
    final Node<E> next = f.next;
    f.item = null;
    f.next = null;
    fisrt = next;
    if(next == null)
      last = null;
    else
      next.prev = null;
    size--;
    modCount++
    return element;
}

public E pop(){
    return removeFirst();
}
//insert the specified element at the begining of list
public void push(E e){
    addFirst(e);
}
```
7. remove相关方法
```java
public E remove(){
    return removeFirst();
}

public E removeFirst(){
    final Node<E> f = first;
    if(f==null)
      throw new NoSuchElementException();
    return unlinkFirst(f);
}

public E removeLast(){
    final Node<E> l =last;
    if(l==null)
      throw new NoSuchElementException();
    return unlinkLast(l);
}

pubic E remove(int index){
    checkElementIndex(index);
    return unlink(node(index));
}

public boolean removeFirstOccurrence(Object o){
    return remove(o);
}

public boolean LastOccurrence(Object o){
    if (o == null) {
      for (Node<E> x = last; x != null; x = x.prev) {
        if (x.item == null) {
                    unlink(x);
                    return true;
                }
       }
    } else {
      for (Node<E> x = last; x != null; x = x.prev) {
        if (o.equals(x.item)) {
          unlink(x);
          return true;
        }
       }
    }
    return false;
}

E unlink(Node<E> x){
    //删除指定的Node节点
    ... ...
}

```
