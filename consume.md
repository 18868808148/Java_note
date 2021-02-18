class MyResource{
    private volatile boolean flag = true;
    private final AtomicInteger atomicInteger = new AtomicInteger();
    BlockingQueue<String> blockingQueue = null;

    public MyResource(BlockingQueue<String> blockingQueue) {
        this.blockingQueue = blockingQueue;
    }

    public void myProd() throws Exception{
        String data = null;
        boolean retValue;
        while (flag){
            data = atomicInteger.incrementAndGet()+"";
            retValue =   blockingQueue.offer(data,2, TimeUnit.SECONDS);
            if (retValue){
                System.out.println(Thread.currentThread().getName() + "\t 生产队列成功");
            }else {
                System.out.println(Thread.currentThread().getName() + "\t  生产队列失败");
            }
            TimeUnit.SECONDS.sleep(1);
        }
        System.out.println(Thread.currentThread().getName() + "\t工厂停工");
    }
    public void consume() throws Exception{
        String result = null;
        while (flag){
           result= blockingQueue.poll(2,TimeUnit.SECONDS);
           if (result == null || result.equalsIgnoreCase("")){
               flag = false;
               System.out.println(Thread.currentThread().getName()+"\t 消费等2s没有消费到停止");
               return;
           }
            System.out.println(Thread.currentThread().getName()+"\t 消费队列成功"+result);
        }
    }
    public void stop() {
        this.flag = false;
    }
}
public class ProdConsumer_BlockQueueDemo {
    public static void main(String[] args) {
        MyResource myResource = new MyResource(new ArrayBlockingQueue<>(10));
        new Thread(()->{
            try {
                myResource.myProd();
            } catch (Exception e) {
                e.printStackTrace();
            }
        },"prod").start();

        new Thread(()->{
            try {
                myResource.consume();
            } catch (Exception e) {
                e.printStackTrace();
            }
        },"consume").start();

        try {
            TimeUnit.SECONDS.sleep(5);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        myResource.stop();
    }
}
