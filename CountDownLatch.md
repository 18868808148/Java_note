让一些线程阻塞知道另一些线程完成一些了操作后唤醒
主要有两个方法，当一个或多个线程调用await方法是，调用线程会被阻塞。
其他线程调用countDown方法会将计数器减一，当计数器的值为零时，因调用await方法的线程会被唤醒
public class CountDownLatchDemo {
    public static void main(String[] args) {
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 1; i <=6 ; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"\t上完自习，离开教室");
                countDownLatch.countDown();
                },String.valueOf(i)).start();
        }
        try {
            countDownLatch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName()+"\t班长锁门");
    }
}