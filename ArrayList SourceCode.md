# ArrayList源码学习
- ### Iterator接口
1. List作为聚合类的接口，Iterator作为迭代器的接口，JDK代码使用了工厂模式和迭代器模式。所谓迭代器模式及即将聚合类的add,get,remove等操作与遍历操作分开来，工厂模式即具体的List实现类可以生成自己需要的迭代器Iterator接口类
2. ArrayList默认的构造函数capacity=10，继承自AbstractList.java的modCount的初始值modCount=0.这个字段JDK的注释如下
```java
/**
     * The number of times this list has been <i>structurally modified</i>.
     * Structural modifications are those that change the size of the
     * list, or otherwise perturb it in such a fashion that iterations in
     * progress may yield incorrect results.
     *
     * <p>This field is used by the iterator and list iterator implementation
     * returned by the {@code iterator} and {@code listIterator} methods.
     * If the value of this field changes unexpectedly, the iterator (or list
     * iterator) will throw a {@code ConcurrentModificationException} in
     * response to the {@code next}, {@code remove}, {@code previous},
     * {@code set} or {@code add} operations.  This provides
     * <i>fail-fast</i> behavior, rather than non-deterministic behavior in
     * the face of concurrent modification during iteration.
     *
     * <p><b>Use of this field by subclasses is optional.</b> If a subclass
     * wishes to provide fail-fast iterators (and list iterators), then it
     * merely has to increment this field in its {@code add(int, E)} and
     * {@code remove(int)} methods (and any other methods that it overrides
     * that result in structural modifications to the list).  A single call to
     * {@code add(int, E)} or {@code remove(int)} must add no more than
     * one to this field, or the iterators (and list iterators) will throw
     * bogus {@code ConcurrentModificationExceptions}.  If an implementation
     * does not wish to provide fail-fast iterators, this field may be
     * ignored.
     */
     protected transient int modCount = 0;
```
3. 主要方法源码
  ```java
  //add(E e)
  public boolean add(E e){
    ensureCapacityInternal(size+1);//size: the number of elements it contains. It's =0 when First add operate
    elementData[size++] = e;
    return true;
  }
  
  private void ensureCapacityInternal(int minCapacity){//when first add minCapacity=1;
    if(elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA){
      minCapacity = Math.max(DEFAULT_CAPACITY,minCapacity);//DEFAULT_CAPACITY=10;
    }
    ensureExplicitCapacity(minCapacity);
  }
  
  private void ensureExplicitCapacity(int minCapacity){
    modCount++;
    if(minCapacity - elementData.length >0)
      grow(minCapacity);
  }
  
  private void grow(int minCapacity){
    //overflow-conscious code 扩容时需要考虑的溢出问题
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);//右移以为一位相当于除以2
    if (newCapacity - minCapacity < 0)
      newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
      newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
  }
  
  private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
        MAX_ARRAY_SIZE;
    }
    
 //get
 public E get(int index){
   rangeCheck(index);
   return elementData(index);
 }
 
 //remove
 public E remove(int index){
   rangeCheck(index);
   modCount++;
   E oldValue = elementData(index);
   int numMoved = size-index-1;
   if(numMoved > 0)
     System.arraycopy(elementData,index+1,elementData,index,numMoved);
   elementData[--size] = null;
   return oldValue;
 }
 
 //remove
 public E remove(Object o){
   if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
        } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
        }
        return false;
 } 
 
 public void fastRemove(int index){
  modCount++;
  int numMoved = size - index - 1;
  if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,numMoved);
        elementData[--size] = null; // clear to let GC do its work
 }
 
 //writeObject/readObject
 /**
 * 不想自动序列化给属性，使用writeObject/readObjecta手动自己设置序列化
 * ArraryList使用transient是因为elementData类似于容器，在自动扩容之后一般都是大于ArrayList实际的size值，
 * 比如elementData.length =10,而这个时候假设实际只有4个值，其他的为null，这个时候如果直接全部序列化会
 * 带来空间上的浪费，特别是当元素非常多的时候
 */
 transient Object[] elementData; 
 
 //Iterator方法
 pubic Iterator<E> iterator(){
   return new Itr();
 }
 
 private class Itr implements Iterator<E>{
   int curosr;
   int lastRet = -1;
   int expectedModCount = modCount;
   public boolean hasNext(){
     return cursor != size;
   }
   
   public E netx(){
     checkForComodification();
     int i = cursor;
     if(i >= size){
       throw new NoSuchElementException();
     }
     //成员内部类访问外部类的成员变量or方法的使用样例,访问外部类成员变量
     Object[] elementData =ArrayList.this.elementData;
     if(i >= elementData.length)
       throw new ConcurrentModificationException();
     cursor = i+1;
     return (E)elementData[lastRet = i];
   }
   
   public void remove(){
     if(lastRet < 0)
       throw new IllegalStateException();
     checkFOromodification();
     try{
       //此处访问外部类的方法
       ArrayList.this.remove(lastRet);
       cursor = lastRet;
       lastRet = -1;
       expectedModCount = modCount;
     }catch(IndexOutOfBoundsException ex) {
       throw new ConcurrentModificationException();
     }
   }
   
   final void checkForComodification(){
     if(modCount != expectedModCount)
       throw new ConcurrentModificationException();
   }
 }
 
  ```
