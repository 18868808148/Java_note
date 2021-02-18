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
      
      
      
synchronized和lock区别
1.synchronized是关键字属jvm层面，ReentrantLock是接口api层面的锁
2.synchronized不需要用户手动释放锁，执行完系统自动释放ReentrantLock需要手动释放锁
3.synchronized不可中断除非异常，ReentrantLock可中断
4.synchronized是非公平锁，ReentrantLock两者都可以，默认非公平锁
5.synchronized没有condition，ReentrantLock可以精确唤醒
class ShareResource{
    private int number = 1;//A:1 B:2 C:3
    private Lock lock = new ReentrantLock();
    private Condition c1 = lock.newCondition();
    private Condition c2 = lock.newCondition();
    private Condition c3 = lock.newCondition();
    public void doA(){
        lock.lock();
        try {
            while (number != 1){
                c1.await();
            }
            for (int i = 1; i <=5 ; i++) {
                System.out.println(Thread.currentThread().getName() + "\t 打印第" + i + "次");
            }
            number = 2;
            c2.signal();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    public void doB(){
        lock.lock();
        try {
            while (number != 2){
                c2.await();
            }
            for (int i = 1; i <=10 ; i++) {
                System.out.println(Thread.currentThread().getName() + "\t 打印第" + i + "次");
            }
            number = 3;
            c3.signal();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    public void doC(){
        lock.lock();
        try {
            while (number != 3){
                c3.await();
            }
            for (int i = 1; i <=15 ; i++) {
                System.out.println(Thread.currentThread().getName() + "\t 打印第" + i + "次");
            }
            number = 1;
            c1.signal();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
public class SyncAndLockDemo {
    public static void main(String[] args) {
        ShareResource shareResource = new ShareResource();
        new Thread(()->{
            for (int i = 0; i <10 ; i++) {
                shareResource.doA();
            }
        },"A").start();
        new Thread(()->{
            for (int i = 0; i <10 ; i++) {
                shareResource.doB();
            }
        },"B").start();
        new Thread(()->{
            for (int i = 0; i <10 ; i++) {
                shareResource.doC();
            }
        },"C").start();
    }
}
