CAS比较并交换 CompareAndSwap
   AtomicInteger
     public final int getAndIncrement() {
           return unsafe.getAndAddInt(this, valueOffset, 1);
       }
   public final boolean compareAndSet(int expect, int update) {
        return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }
   Unsafe
   public final int getAndAddInt(Object var1, long var2, int var4) {
            int var5;
            do {
                var5 = this.getIntVolatile(var1, var2);
            } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
    
            return var5;
   }
   常用原子类：
   AtomicReference<> 原子应用类
   AtomicStampedReference<> 带版本号的 
   UnSafe是CAS的核心类，CAS是一种系统源语，在执行过程中不允许被中断，保证了数据原子性
   缺点：
   循环时间长，开销大
   只能保证一个共享变量的原子性
   ABA问题：解决方案 AtomicStampedReference
    public boolean compareAndSet(V   expectedReference,
                                    V   newReference,
                                    int expectedStamp,
                                    int newStamp) {
           Pair<V> current = pair;
           return
               expectedReference == current.reference &&
               expectedStamp == current.stamp &&
               ((newReference == current.reference &&
                 newStamp == current.stamp) ||
                casPair(current, Pair.of(newReference, newStamp)));
       }
   