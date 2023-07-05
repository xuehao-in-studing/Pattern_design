# Pattern_design

## 一、单例模式
单例模式的使用意图：一个类仅能有一个实例，并且提供一个全局访问点。  
单例模式的主要解决问题：解决全局使用的类的频繁创建与销毁  
单例模式的使用场景：  
1. 想要节省系统资源，例如I/O和数据库的连接
2. 控制实例数目，例如文件管理的时候应该只允许一个实例去写，多个实例去写会导致写入冲突

创建单例模式的几种方式：
1. 懒汉式，不加锁
单例类代码：
main：
![image](https://github.com/xuehao-in-studing/Pattern_design/assets/102791379/d97cb4dc-615d-4846-b11b-4ade37a07f38)  
运行结果：
![image](https://github.com/xuehao-in-studing/Pattern_design/assets/102791379/28549f34-0a3d-4e31-b11a-578c1c08e9dc)  
可以看到
