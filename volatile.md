volatile是Java虚拟机提供对的轻量级同步机制
    保证可见性：由于JVM运行程序额实体是线程，每个线程创建时JVM都会为其创建一个工作内存（线程对的私有数据区域），Java内存模型中规定所有的变量都储存在主内存
    ，主内存是共享内存区域。当线程对变量进行操作时，首先从主内存拷贝到自己的工作内存，然后对变量进行操作，操作完将变量写回主内存。并告知其他线程
    不保证原子性：number++在多线程下是非现场安全的 因为多线程是由cup调度的，会出现数据丢失 解决方案使用原子包装类：AtomicInteger.getAndIncrement
    禁止指令重排
使用场景：
    懒汉单例：
    public class SingleTon{
        private static volatile SingleTon instance = null;
        
        private SingleTon(){}
        
        public static SingleTon getInstance(){
            if(instance == null){
                synchronizedd (SingleTon.class){
                  if(instance == null){
                                instance = new SingleTon();
                            }
                }
            }
            return instance;
        }
    }
    