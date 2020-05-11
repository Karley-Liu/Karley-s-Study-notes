# 单例设计模式

## 定义

保证一个类只有一个实例，并且提供一个全局访问点

## 场景

重量级对象，不需要多个实例，例如线程池，数据库连接池

## 示例图

![image-20200221095128716](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200221095128716.png)

```
public	类名{

private	类型	属性；

​	private	class	函数名（）{

​		public	class	getInstance(){

​			return 单例实例

​		}

​	}

}
```

## 懒汉模式

延迟加载，只有在使用的时候才开始实例化

1. 线程安全问题
2. double	check	加锁优化
3. 编译器(JIT)，CPU有可能对指令进行重排序，导致使用到尚未初始化的实例，可以通过添加volatile关键字进行修饰，对于volatile修饰的字段，可以防止指令重排。

```java
public class LazySingletonTest{
    public static void main(String[] args){
        //LazySingleton instance = LazySingleton.getInstance();
        //LazySingleton instance1 = LazySingleton.getInstance();
        //System.out.println(instance == instance1);
        new Thread( () -> {	//开多线程
            LazySingleton instance = LazySingleton.getInstance();
            System.out.println(instance);
        }).start();
        new Thread( () -> {
            LazySingleton instance = LazySingleton.getInstance();
            System.out.println(instance);
        }).start();
    }
}
class LazySingleton{	//定义singleton
    private static LazySingleton instance;	//定义私有静态属性
    private LazySingleton(){	//提供私有的构造函数，避免从外部定义对应实例的创建
        
    }
    public static LazySingleton getInstance(){	//全局访问点，一个静态方法
        if(instance == null){	//实例为空，实例化一个instance
			synchronized(LazySingleton.class){	//在实例为空的时候加锁
                if(instance == null){	//再去check,instance为空再去实例化
                 instance = new LazySingleton();
                    //字节码层
                    //JIT
                    //1.分配空间
                    //2.初始化
                    //3.引用赋值
                }
            }
        }
        return instance;
    }
}
```

多个线程同时实例化一个实例，违背单例模式，在静态方法中添加synchronized，可获取同一个实例。多个线程要求实例时，第一个加锁已经实例化，后面的线程直接拿到实例

```shell
#javap	//反汇编工具
#javap -v
```

## 饿汉模式

类加载的初始化阶段就完成了实例的初始化。本质就是借助于jvm类加载机制，保证实例的唯一性。

类加载过程：

1. 加载二进制数据到内存中，生成对应的Class数据结构。
2. 连接：a.验证，b.准备(给类的静态成员变量赋默认值)，c.解析
3. 初始化：给类的静态变量赋初值

只有在真正使用对应的类时，才会触发初始化，如(当前类是启动类即main函数所在类，直接进行new操作，访问静态属性、访问静态方法，用反射访问类，初始化一个类的子类等)

``` java
public class HungrySingletonTest{
    public static void main(String[] args){
        HungrySingleton instance = HungrySingleton.getInstance();
        HungrySingleton instance1 = HungrySingleton.getInstance();
        
    }
}
//饿汉模式
class HungrySingleton{
    private static HungrySingleton instance = new HungrySingleton();	//静态类实例化
    private HungrySingleton(){
        
    }
    public static HungrySingleton getInstance(){
        return instance;
    }
}
```

## 静态内部类

1. 本质上是利用类的加载机制来保证线程安全
2. 只有在实际使用的时候才会触发类的初始化，所以也是懒加载的一种形式

```java
public class InnerClassSingletonTest{
    public static void main(String[] args){
        InnerClassSingleton instance = InnerClassSingleton.getInstance();
        InnerClassSingleton instance1 = InnerClassSingleton.getInstance();
        sout
    }
}
class InnerClassSingleton{
    private static class InnerClassHolder{
        private static InnerClassSingleton instance = new InnerClassSingleton();
    }
    private InnerClassSingleton(){
        
    }
    public static InnerClassSingleton getInstance(){
        return InnerClassHolder.instance;
    }
}
```

