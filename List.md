集合类线程不安全之并发修改异常
导致原因：并发争抢修改导致，一个线程正在写入，另一个线程来抢夺导致数据不一致异常
解决方案1：使用Vector
解决方案2：Collections.synchronizedList
解决方案3：CopyOnWriteArrayList 读写分离
  /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return {@code true} (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }  
  CopyOnWriteArraySet
  ConcurrentHashMap
  JDK1.7ConcurrentHashMap是由Segment数组结构和HashEntry数组结构组成
  JDK1.8的实现已经摒弃了Segment的概念，而是直接用Node数组+链表+红黑树的数据结构来实现，
  并发控制使用Synchronized和CAS来操作，整个看起来就像是优化过且线程安全的HashMap