队列
阻塞队列
当阻塞队列是空时，从队列中获取元素的操作会被阻塞
当阻塞队列是满时，往队列中插入元素的操作会被阻塞
好处：我们不需要关心什么时候需要阻塞线程，什么时候唤醒线程，由队列自动操作了
ArrayBlockingQueue:基于数组结构的有界阻塞队列，按FIFO（先进先出）原则对元素进行排序
LinkedBlockingQueue:基于链表结构的阻塞 队列，按FIFO（先进先出）排序吞吐量高于ArrayBlockingQueue
SynchronousQueue:不能储存元素的阻塞队列。每个插入操作必须等到另一个线程的调用移除操作，否则插入操作一直
处于阻塞状态，吞吐量 通常要高



class ShareData{
    private int number = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();
    public void increment(){
        lock.lock();
        try {
            while (number != 0 ){
                //等待不能生产
                try {
                    condition.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            number++;
            System.out.println(Thread.currentThread().getName() + "\t" + number);
            condition.signalAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    public void decrement(){
        lock.lock();
        try {
            while (number == 0 ){
                //等待不能生产
                try {
                    condition.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            number--;
            System.out.println(Thread.currentThread().getName() + "\t" + number);
            condition.signalAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
public class ProdConsumer_TraditionDemo {
    public static void main(String[] args) {
        ShareData shareData = new ShareData();
        new Thread(()->{
            for (int i = 1; i <=5 ; i++) {
                shareData.increment();
            }
        },"AAA").start();
        new Thread(()->{
            for (int i = 1; i <=5 ; i++) {
                shareData.decrement();
            }
        },"BBB").start();
    }
}