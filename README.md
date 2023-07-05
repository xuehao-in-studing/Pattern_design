# Pattern_design

## 一、单例模式
单例模式的使用意图：一个类仅能有一个实例，并且提供一个全局访问点。  
***主要解决问题：解决全局使用的类的频繁创建与销毁**  
单例模式的使用场景：  
1. 想要节省系统资源，例如I/O和数据库的连接
2. 控制实例数目，例如文件管理的时候应该只允许一个实例去写，多个实例去写会导致写入冲突

创建单例模式的几种方式：
1. 懒汉式，不加锁  
单例类代码：  
```
    public class SingleInstance
    {
        // 单例、实例
        private static SingleInstance instance;

        private SingleInstance() { }
        public static SingleInstance GetInstance()
        {
            if (instance == null)
            {
                Thread.Sleep(500);
                // 计数器自增
                Counter.Increment();
                Console.WriteLine($"第{Counter.GetCount()}进入");
                instance = new SingleInstance();
            }
            Console.WriteLine("实例化成功...");
            Thread.Sleep(500);
            return instance;
        }
    }
```
构造器为private，防止外部访问，通过GetInstance获得该类的唯一实例。  

main：  
```
    SingleTon[] instanceArr = new SingleTon[5];
    for (int i = 0; i < 5; i++)
    {
        // 在五个线程中同时获得实例
        // 注：这里必需传入参数i，因为线程调用是随机的，如果将i作为index的参数传入，可能当i为5时调用线程
        // 从而导致索引超限错误
        new Thread((index) =>
        {
            instanceArr[(int)index] = SingleTon.GetInstance();
        }).Start(i);
    }
    for (int i = 0; i < 4; i++)
    {
        if (Object.ReferenceEquals(instanceArr[i], instanceArr[i + 1]))
        {
            Console.WriteLine("指向同一个对象");
        }
        else
        {
            Console.WriteLine("指向不同对象");
        }
    }
```
在main线程中新建五个线程，每个线程都去获取实例，这时候会产生实例冲突。   

运行结果：  
![image](https://github.com/xuehao-in-studing/Pattern_design/assets/102791379/241c23d4-0c52-4e4e-a798-e0b4702f91da)  
显而易见的是，所有的引用变量都**指向同一个内存地址**，也就是**只有一个实例对象**，这也就是为什么叫做单例模式。  
可以看到如果不加锁，当多个线程同时调用时，就会线程挂起，线程抢夺，从而可能导致前一个线程已经实例化了该类，但是还没有写入寄存器，其他线程这个时候就会再次实例化。  

2. 双重锁定  
加锁代码：  
```
public class SingleTon
    {
        // 实例对应的锁对象，实例化之后就不可更改，因此该锁代表该实例
        // 对lockobj加锁也就是对实例加锁
        private readonly static object lockObj = new object();
        private static SingleTon instance;
        private SingleTon()
        {
        }
        public static SingleTon GetInstance()
        {
            // 当实例对象还没有被创建时进行创建
            if (instance == null)
            {
                lock (lockObj)
                {
                    if (instance == null)
                    {
                        Counter.Increment();
                        Console.WriteLine($"第{Counter.GetCount()}次进入" + "线程加锁，正在创建实例......");
                        //Thread.Sleep(500);
                        instance = new SingleTon();
                    }
                }
            }
            Console.WriteLine("实例创建完成，正在获取实例......");
            //Thread.Sleep(500);
            // 实例创建后直接返回
            return instance;
        }
    }
```
该代码两次确定单例是不是已经被创建，防止两个线程都进入了lock之前的代码，而当一个线程创建完毕释放锁后，另一个线程再次实例化。

main和上面基本相同。  
结果：   
![image](https://github.com/xuehao-in-studing/Pattern_design/assets/102791379/7e013e97-a795-4213-83fc-69f5338a4461)  

3. 饿汉式：  
```
    public class SingleTon
    {
        // 调用类时直接创建实例对象
        private static SingleTon instance = new SingleTon();

        private SingleTon() { }

        public static SingleTon GetInstance()
        {
            return instance;
        }
    }
```
优点：代码简洁，避免多线程同步问题，没有锁，效率高  
缺点：产生垃圾对象  

