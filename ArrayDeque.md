### ArrayDeque实现了Deque接口，即实现了双端队列，ArrayDeque是使用数组的方式来实现的

### 底层使用了循环数组的概念，所以head并非只会等于0，head < tail也不是一直都成立; 判断队列已满的调节也存在两种情况

### 主要的方法
1. 构造函数
```java
//默认的elements.length=16,并且最小的值为8
public ArryDeque(){
    elements = new Object[16];
}

public ArrayDeque(Collection<? extends E> c){
    allocateElements(c.size());
    addAll(c);
}

public ArrayDeque(int numElements){
    allocateElements(numElements);
}

private void allocateElements(int numElements){
    int initialCapacity = MIN_INITIAL_CAPACITY;
    //ArrayDeque对elements.length的值全部是2的幂次，下面的代码是找到最好的2的幂次方的值来申请elements
    if(numElements >= initialCapacity){
        initialCapacity = numElements;
        initialCapacity |= (initialCapacity >>>  1);
        initialCapacity |= (initialCapacity >>>  2);
        initialCapacity |= (initialCapacity >>>  4);
        initialCapacity |= (initialCapacity >>>  8);
        initialCapacity |= (initialCapacity >>> 16);
        initialCapacity++;
        
        if (initialCapacity < 0)   // Too many elements, must back off
        initialCapacity >>>= 1;// Good luck allocating 2 ^ 30 elements
    }
    elements = new Object[initialCapacity];
}
```
2. add/addFirst/addLast/方法
```java
public boolean add(E e){
    addLast(e);
    return true;
}

/**
*下面的两个方法都有判断是否需要扩容，判断deque满了的
*两种情况
*  1. head == tail 
*  2. tail+1 & elements.length -1 == head
*
*/
public addFirst(E e){
    if(e == null)
      throw new NullPointerExcetion();
    elements[head = (head-1) & (elements.length-1)] = e;
    if(head == tail)
      doubleCapacity();
}

public addLast(E e){
    if(e == null)
      throw new NullPointerExcetion();
    elements[tail] = e;
    if( (tail = (tail+1)) & (elements.length-1) ) == head )
      doubleCapacity();
}  

private void doubleCapacity() {
    assert head == tail;
    int p = head;
    int n = elements.length;
    int r = n - p; // number of elements to the right of p
    int newCapacity = n << 1;
    if (newCapacity < 0)
        throw new IllegalStateException("Sorry, deque too big");
    Object[] a = new Object[newCapacity];
    System.arraycopy(elements, p, a, 0, r);
    System.arraycopy(elements, 0, a, r, p);
    elements = a;
    head = 0;
    tail = n;
    }
```
3. 常用方法总结

方法 | 说明 | 类型
---|--- | ---
add/addFisrt/addLast | add直接调用addLast，默认队尾插入元素 | 属于add操作 |
offer/offerFirst/offerLast |  offer直接调用offerLast，并且offerLast和offerFirst分别直接调用addLast和addFirst | 属于add操作
peek/peekFirst/peekLast | peek直接调用peekFirst,peekFisrt & peekLast就是获取队列的元素，等同于getFirst & getLast;只是getFisrt/getLast会在判断返回结果为null是抛出异常，而peekFirst/peekLast却不会，直接返回null | 属于get操作
poll/pollFirst/pollLast| poll直接调用pollFirst，Retrieves and removes the head of the queue,返回队列头/尾元素，并将其删除| 属于delete操作|
pop/push/remove/removeFirst/removeLast | pop直接调用removeFirst，removeFirst调用了pollFirst; pushh直接调用了addFirst | 属于delet操作|

4. 从上表可以看出, ArrayDeque实现了stack的功能，提供了pop/push函数实现了先进先出的栈功能, Java已经不推荐使用Stack类,推荐使用更高效的ArrayDeque.另外需要使用队列时候首先使用ArrayDeque(其次是LinkedList,上文已提到LinkedList也实现了Deque接口)
