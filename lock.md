Java里的锁
    1.公平锁和非公平锁
    ReentrantLock 默认非公平
    公平锁：多个线程按照申请锁的顺序来获取锁（先来后到，排队）
    非公平锁：多个线程不按照申请锁的顺序获取锁 （插队）
    2.可重入锁（递归锁）synchronized、ReentrantLock
    3.自旋锁是指尝试获取锁的线程不会阻塞，而是采用循环的方式尝试获取锁，
    好处是减少线程上下文切换的消耗，缺点消耗cpu
    //Unsafe
     public final int getAndAddInt(Object var1, long var2, int var4) {
            int var5;
            do {
                var5 = this.getIntVolatile(var1, var2);
            } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
           return var5;
      }