# Java基础

## 解决接口多重继承中名字冲突的问题

子接口继承多个父接口，方法重名子类会覆盖父类  无论default

```java
interface A{
    default void eat(){
        System.out.println("2");
    }
}
interface B{
    void eat();
}

interface C extends A,B{
    default void eat(){

    }
}
public class Me implements C{
   

    public static void main(String[] args) {
        new Me().eat();//输出为空
    }
}
```

当在类中实现多个接口（A,B） A,B有重名方法eat，如果不为default不会冲突  如果都为default可以通过调用super指定实现哪个

```java
interface A{
    default void eat(){
        System.out.println("2");
    }
}
interface B{
    default void eat(){

    }
}

interface C extends A,B{
    default void eat(){
        B.super.eat();
    }
}
public class Me implements A,B{
    @Override
    public void eat() {
        A.super.eat();
    }
    public static void main(String[] args) {
        new Me().eat();
    }
}
```

## 抽象类和接口的区别

### 抽象类

1. 不能被实例化
2. **abstact**修饰
3. 抽象方法修饰符只能为**protected**和**public**
4. 子类继承抽象类，必须实现父类的抽象方法，否则子类必须定义为抽象类
5. 抽象类可以包含属性、方法、构造函数，但构造方法不能用于实例化

### 接口

1. 接口可以包含变量和方法  **变量**隐式指定为**public static final**，**方法**指定为**public  abstract**
2. 接口支持多继承，一个接口可以继承多个extends，间接解决java多继承问题
3. 一个类可以实现多个接口
4. 可以用default写一个默认方法  有冲突用super

### 异同

相同

1. 都不能被实例化
2. 必须实现他们的方法

不同

1. 一个类可以实现多个接口，一个类只能继承一个抽象类
2. 接口成员变量默认为**public static final**，必须赋初值，不能被修改；其所有的成员方法都是**public、abstract**的。抽象类中成员变量默认default，可在子类中被重新定义，也可被重新赋值；抽象方法被abstract修饰，不能被private、static、synchronized和native等修饰，必须以分号结尾，不带花括号

## 说一下常见的异常

1. NullPointerException        空指针异常
2. ClassNotFoundException  不能加载相关类
3. ArrayIndexOutOfBoundsException  数组下标越界
4. ConcurrentModificationException 并发修改异常
5. OutOfMeomoryError  堆溢出溢出
6. StackOverFlowError  栈溢出

## throw和throws

throws跟在方法后面，表示方法可能产生的异常

throw在方法体内，主动抛出异常

```java
throw new RuntimeException("a的值大于0，不符合要求")
```

## ReadTimeout和ConnectionTimeout

1.  ConnectTimeout.java  意思是用来建立连接的时间。如果到了指定的时间，还没建立连接，则报异常
2.  ReadTimeout.java  意思是已经建立连接，并开始读取服务端资源。如果到了指定的时间，没有可能的数据被客户端读取，则报异常



# JAVA并发

## 讲一下线程状态

New  新建状态

Runnable  包含ready和run状态

Blocked 阻塞状态

Waiting  等待 wait

Time_Waiting  超时等待  wait(tiem)

Terminated 结束状态

## 死锁和活锁、 饥饿和无锁

**死锁**：多个线程占有资源，并同时向对方请求的资源进行请求，产生循环资源依赖

**活锁**：活锁是拿到资源却又相互释放不执行。当多线程中出现了相互谦让，都主动将资源释放给别的线程使用，这样这个资源在多个线程之间跳动而又得不到执行，这就是活锁。

**饥饿**：我们知道多线程执行中有线程优先级这个东西，优先级高的线程能够插队并优先执行，这样如果优先级高的线程一直抢占优先级低线程的资源，导致低优先级线程无法得到执行，这就是饥饿。当然还有一种饥饿的情况，一个线程一直占着一个资源不放而导致其他线程得不到执行，与死锁不同的是饥饿在以后一段时间内还是能够得到执行的，如那个占用资源的线程结束了并释放了资源。

**无锁**：无锁，即没有对资源进行锁定，即所有的线程都能访问并修改同一个资源，但同时只有一个线程能修改成功。无锁典型的特点就是一个修改操作在一个循环内进行，线程会不断的尝试修改共享资源，如果没有冲突就修改成功并退出否则就会继续下一次循环尝试。所以，如果有多个线程修改同一个值必定会有一个线程能修改成功，而其他修改失败的线程会不断重试直到修改成功

## wait和sleep的区别

1. sleep()是Thread类的静态方法，wait()是object的方法
2. sleep()暂停线程，wait()会让出锁资源
3. sleep()任何地方都能用，wait要在synchronized里面

## notify和notifyAll

调用notify时，只有一个等待线程会被唤醒而且它不能保证哪个线程会被唤醒，这取决于线程调度器。

调用notifyAll方法，那么等待该锁的所有线程都会被唤醒，会竞争锁

# Springboot

## 循环依赖

### 是什么

我现在有一个ServiceA需要调用ServiceB的方法，那么ServiceA就依赖于ServiceB，那在ServiceB中再调用ServiceA的方法，就形成了循环依赖。Spring在初始化bean的时候就不知道先初始化哪个bean就会报错

```java
public class ClassA {
    @Autowired
    ClassB classB;
}
 
public class ClassB {
    @Autowired
    ClassA classA ;
} 
```

### 如何解决？

1. 重构代码
2. 在你的配置文件中，在互相依赖的两个bean的任意一个加上lazy-init属性

```xml
<bean id="ServiceDependent1" class="org.xyz.ServiceDependent1" lazy-init="true">
      <constructor-arg ref="Service"/>
</bean>
    
<bean id="ServiceDependent2" class="org.xyz.ServiceDependent2" lazy-init="true">
      <constructor-arg ref="Service"/>
</bean>
```

3. 注入bean时，在互相依赖的两个bean上加上@Lazy注解也可以

```java
    @Autowired
    @Lazy
    private ClassA classA;
    @Autowired
    @Lazy
    private ClassB classB;
```

## @Autowire和@Resource区别

1. @Autowire是spring提供的，@Resource是Java自己的
2. **@Autowired** 是根据 类型 （byType）注入的 ，然后在找到type类型的bean时，如果发现有异常（不唯一等），会再去根据name去找bean（默认名字是变量名字）注入（这个时候应该用@Qualifier指定名字by name）

假设两个实现类

![img](https://img-blog.csdnimg.cn/20201023100335856.png)

![img](https://img-blog.csdnimg.cn/20201023100929450.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1Mzg3OTQw,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20201023102236243.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1Mzg3OTQw,size_16,color_FFFFFF,t_70)

3. **@Resource** **默认是** 根据 名字（byName）注入的 ，但是如果使用的时候，跟上文一样啥都没有指定，那么就是先byName 默认方式去找（userService），发现根本没有这个userService， 因为我们 注入到bean容器里面 是 这两个玩意（@Servcie）

![img](https://img-blog.csdnimg.cn/20201023104737683.png)

​      而且咱们使用@Servcie 注入这俩玩意没有起别名，那么就是默认 首字母变小写 当做注入的bean名字

那么回到 @Resource ，它根据可靠信息 name 名称 （ userService  ），找不到bean；

那么就会根据 type  类型 （UserService） 去spring容器里面找找，有没有这种类型的bean；

结果发现了 两个类型都是 UserService 的 两个倒霉孩子  userServiceImpl,userServiceNewImpl ；

所以就报错



