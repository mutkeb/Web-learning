# 第一章——Lambda表达式

### 简介

格式：f -> {}

## 无类型参数

### 类型识别

> 系统会根据上下文自动识别类别

### 多参数

```java
(student1, student2) -> {}
```

> 用小括号包裹多参数

### 无参数

```java
() -> {}
```

> 必须使用小括号

### 单条执行语句

> 可以不加大括号{}包裹

```java
s -> System.out.println(s);
```

## 有类型参数

> 如果代码比较复杂，而为了易阅读、易维护，也可以为参数变量指定类型

```java
fruits.forEach((Fruit f) -> {
  System.out.println(f.getName());
});
```

> 如果只有一个参数，也必须使用小括号包裹

## 引用外部变量

### 规范一

引用的局部变量不允许被修改

> 即使写在表达式后面也不行

```java
List<Fruit> fruits = Arrays.asList(......);
String message = "水果名称：";

fruits.forEach(f -> {
  System.out.println(message + f.getName());
});

message = "";
```

### 规范二

参数不能与局部变量同名

```java
String f = "水果名称：";
fruits.forEach(f -> {});
```

## 双冒号（：：）操作符

```java
names.forEach(System.out::println);
```

使用：：时，系统每次遍历取得的元素会自动作为参数传递给打印当方法

相当于是

n -> {System.out.println(n);}

### 不同用法

#### 用法一

静态方法调用

使用LambdaTest::print代替f -> LambdaTest.print(f)

#### 用法二

非静态方法调用

```java
fruits.forEach(new LambdaTest()::print);
```

需要调用实例对象

#### 用法三

多参数

```java
Collections.sort(students, (student1, student2) -> {
  // 第一个参数的学号 vs 第二个参数的学号
  return student1.getRollNo() - student2.getRollNo();
});
```

将该过程定义为一个方法

```java
private static int compute(Student s1, Student s2) {
  ... ...
  ... ...
}
```

就可以简写为

```java
Collections.sort(students, SortTest::compute);
```

#### 用法四

父类方法

```java
import java.util.Arrays;
import java.util.List;

public class LambdaTest extends LambdaExample {
  public static void main(String[] args) {
    List<Fruit> fruits = Arrays.asList(
            new Fruit("香蕉"),
            new Fruit("苹果"),
            new Fruit("梨子"),
            new Fruit("西瓜"),
            new Fruit("荔枝")
    );

    LambdaTest at = new LambdaTest();
    at.print(fruits);
  }

  public void print(List<Fruit> fruits){
    fruits.forEach(super::print);
    }
}

class LambdaExample {
    public void print(Fruit f){
        System.out.println(f.getName());
    }
}
```

# 第二章——Stream API

## 简介

主要是对集合中的数据进行各种操作，增加了集合对象的功能

> 与InputStream完全不同，是不同的概念，经常与Lambda表达式配合使用

## 流迭代

### 创建流

#### 直接创建

```java
import java.util.stream.Stream;

Stream<String> stream = Stream.of("苹果", "哈密瓜", "香蕉", "西瓜", "火龙果");
```

#### 由数组转化

```java
String[] fruitArray = new String[] {"苹果", "哈密瓜", "香蕉", "西瓜", "火龙果"};
Stream<String> stream = Stream.of(fruitArray);
```

### 由集合转化

```java
List<String> fruits = new ArrayList<>();
fruits.add("苹果");
fruits.add("哈密瓜");
fruits.add("香蕉");
fruits.add("西瓜");
fruits.add("火龙果");
Stream<String> stream = fruits.stream();
```

### 迭代流

Stream提供的迭代方式也叫做forEach()

```java
Stream<String> stream = Stream.of("苹果", "哈密瓜", "香蕉", "西瓜", "火龙果");
stream.forEach(System.out::println);
```

> 集合类的forEach和流的forEach方法无关，仅仅是方法名相同

## 流数据过滤

#### filter（）方法

```java
List<Pupil> pupils = new ArrayList<>();
// 这里假设小学生数据对象已经存入了

pupils.stream()
    .filter(pupil -> pupil.getAverageScore() >= 80 && pupil.getViolationCount() < 1)
    .forEach(pupil -> {System.out.println(pupil.getName());});
```

## 流数据映射

![](D:\编程日志\j5-2-5-2.svg)

```java
numbers.stream()
    .map(num -> {
        return num * num;
    })
    .forEach(System.out::println);
```

map（）方法通常称作映射，其作用是用新的元素替掉流中原来相同位置的元素。相当于每个对象都经理一次转换

#### 优点

映射后的对象类型，可以与流中原始的对象类型不一致。极大的提供了灵活性、扩展性。

#### 特例写法

如果替换语句简单，系统能自动识别需要返回的值

```java
.map(num -> num * num)
```

## 流数据排序

```java
students.stream()
    // 实现升序排序
    .sorted((student1, student2) -> {
        return student1.getRollNo() - student2.getRollNo();
    })
    .forEach(System.out::println);
```

> student1指代后一个元素，student2指代前一个元素

#### 特例写法

```java
.sorted((student1, student2) -> student1.getRollNo() - student2.getRollNo())
```

返回非整数表示两个相比较得元素需要交换位置，返回正数则不需要

## 流数据摘取

用limit来限制取的元素数目

```java
numbers.stream()
    .sorted((n1, n2) -> n2 - n1)
    .limit(3)
    .forEach(System.out::println);
```

# 第三章——并行数据

## 流合并（一）

```java
import java.util.Arrays;

int sum = numbers.stream()
    .reduce((a, b) -> a + b)
    .get();

System.out.println("1-10求和 : " + sum);
```

> reduece方法得作用是合并了所有得元素，终止计算出一个结果
>
> get方法返回最终得整数值

reduce（）方法的参数

* a在第一次执行计算语句a+b时，指代流的第一个元素；然后充当缓存作用以存放本次计算结果
* b参数第一次执行语句时指代流的第二个元素。此后一次指代流的每个元素

![](D:\编程日志\j5-3-1-1.svg)

## 流合并（二）

```java
Student result = students.stream()
    .reduce(
        (a, b) -> {
            a.setMidtermScore(a.getMidtermScore() + b.getMidtermScore());
            return a;
        }
    )
    .get();
```

第一个Student对象由于充当了缓存角色，正确性被破坏了

此时的总分变成一个Student的了

### 解决办法

自己new一个对象充当缓存角色，而不是使用流中的原始对象

```java
Student result = students.stream()
    .reduce(new Student("", 0),
        (a, b) -> {
            a.setMidtermScore(a.getMidtermScore() + b.getMidtermScore());
            return a;
        }
    );
```

> 此时a变量专门指代缓存角色的对象，最后输出的result即带有总分，且无姓名
>
> 

## 流收集

```java
import java.util.stream.Collectors;

List<String> numResult = numbers.stream()
    .sorted((n1, n2) -> n2 - n1)
    .limit(3)
    .map(a -> "" + a)
    .collect(Collectors.toList());

String string = String.join("-", numResult);
System.out.println("字符串是: " + string);
```

collect（）方法的做哦那个就是收集元素。

因为利用map转化成了String类型，所以返回值类型是List<String>而不是List<Integer>

## 并行流

不再调用stream（）方法，改为调用parallelStream（）即可

流中的每个数据元素之间，有逻辑依赖关系的时候，不适合使用并行计算

## 并行流的性能意外

### 原因

1. 硬件太差。CPU核数很低
2. 任务简单。因为多线程的管理也是会消耗CPU、内存等资源的，可能比任务本身的开销更大

# 第四章——常用设计模式

## 作用

有助于提示代码质量，也有助于提示程序员的设计能力

## 单例模式、

### 问题

班主任类

```java
public class ClassMaster {
  private String id;
  // 班主任名称
  private String name;
  private String gender;
}
```

```java
public class Classes {

  public static void main(String[] args) {
    // 创建班主任实例
    ClassMaster classMaster = new ClassMaster();
    // 创建班主任实例2
    ClassMaster classMaster2 = new ClassMaster();
  }
}
```

在Classes可以创建多个ClassMaster实例。这就违背了要求，没有做到班主任类只有一个实例对象。

### 解决

```java
public class ClassMaster {
  private String id;
  // 班主任名称
  private String name;
  private String gender;
  private ClassMaster() {
  }
}
```

将构造函数设置为私有的，除了ClassMaster自己，其他任何类都不能实例化ClassMaster对象，就能保证ClassMaster的实例是全局唯一的。

```java
public class ClassMaster {
  private String id;
  // 班主任名称
  private String name;
  private String gender;

  // 唯一实例
  private static ClassMaster instance = new ClassMaster();

  private ClassMaster() {
  }
}
```

### 访问实例

```java
public class ClassMaster {
  private String id;
  // 班主任名称
  private String name;
  private String gender;

  // 唯一实例
  private static ClassMaster instance = new ClassMaster();

  private ClassMaster() {
  }

  // 外部类可以通过这个方法访问唯一的实例
  public static ClassMaster getInstance() {
    return instance;
  }
}
```

像@Autowired也是单例模式的体现

## 简单工厂模式

![](D:\编程日志\j5-4-3-2.svg)

### 工厂实现

```java
public class FruitFactory {
    public static Fruit getFruit(Customer customer) {
        Fruit fruit = null;
        if ("sweet".equals(customer.getFlavor())) {
            fruit = new Watermelon();
        } else if ("acid".equals(customer.getFlavor())) {
            fruit = new Lemon();
        } else if ("smelly".equals(customer.getFlavor())) {
            fruit = new Durian();
        }

        return fruit;
    }
}
```

## 抽象工厂模式

多种产品

![](D:\编程日志\j5-4-4-1.svg)

```java
public interface SnacksFactory {
    // 取得水果
    public Fruit getFruit(Customer customer);
    // 取得饮料
    public Drink getDrink(Customer customer);
}
```

```java
public class FruitFactory implements SnacksFactory {
    public Fruit getFruit(Customer customer) {
        Fruit fruit = null;
        if ("sweet".equals(customer.getFlavor())) {
            fruit = new Watermelon();
        } else if ("acid".equals(customer.getFlavor())) {
            fruit = new Lemon();
        } else if ("smelly".equals(customer.getFlavor())) {
            fruit = new Durian();
        }

        return fruit;
    }

    public Drink getDrink(Customer customer) {
        return null;
    }
}
```

### 工厂的工厂

```java
public class SnacksFactoryBuilder {
    public SnacksFactory buildFactory(String choice) {
        if (choice.equalsIgnoreCase("fruit")) {
            return new FruitFactory();
        } else if (choice.equalsIgnoreCase("drink")) {
            return new DrinkFactory();
        }
        return null;
    }
}
```

SnacksFactoryBuilder的buildFactory（）方法并不是static的，因为在复杂场景下尽量不要使用类方法，实例方法可以被继承，扩展性较好

## 工厂模式结合Spring工程

在使用Spring框架的时候，可以为SnacksFactoryBuilder加上@Component注解，让框架管理实例

```java
@Component
public class SnacksFactoryBuilder {
    public SnacksFactory buildFactory(String choice) {

    }
}
```

任何需要使用工厂的地方，只需要使用@@Autowired注解让框架自动注入实例即可

## 观察者模式

### 观察对象

```java
import java.util.Observable;

public class WeatherData extends Observable {
    // 城市
    private String cityName;
    // 时间
    private String time;
    // 温度
    private String temp;

    // 城市固定了就不变了
    public WeatherData(String cityName) {
        this.cityName = cityName;
    }

    // 打印天气信息
    public String toString() {
        return cityName + "，" + LocalDate.now().toString() + " " + time + "，气温：" + temp + "摄氏度。";
    }

    public String getCityName() {
        return cityName;
    }

    public String getTime() {
        return time;
    }

    public String getTemp() {
        return temp;
    }
}
```

继承了Ovservable。表示被观察的类

watchData是被观察者。

因为要使数据变化后发起通知，所以增加一个新的方法专门处理

```java
import java.util.Observable;

public class WeatherData extends Observable {
    /**
     * 一个城市的气温在某个时刻发生了变化
     */
    public void changeTemp(String time, String temp) {
        if(time == null || temp == null) {
            // 输入数据为空是有问题的，不处理
            return;
        }

        // 与原数据不同，说明发生了变化
        if(!time.equals(this.time) || !temp.equals(this.temp)) {
            // 标记变化
            super.setChanged();
            this.time = time;
            this.temp = temp;
            // 发出通知，参数是额外的信息
            super.notifyObservers("温度变化已通知");
        }
    }
}
```

父类Observable提供的方法setChanged（）就是标记被观察者对象发生了变化

notifyObservers()就是发出通知，父类提供的。

### 观察者

```java
import java.util.Observable;
import java.util.Observer;

public class WeatherObserver implements Observer {
    private String name;

    @Override
    public void update(Observable o, Object arg) {
        System.out.print(this.name + "观察到天气变化为：");
        System.out.print(o.toString());
        System.out.println(" " + arg);
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

要实现update（）方法。第一个参数是被观察者对象，第二个就是额外的信息。具体说就是调用`super.notifyObservers()`传入的参数对象，传入什么对象，arg就是什么值

### 运行程序

```java
public class WeatherTest {
    public static void main(String[] args) {
        // 在天气变化后发邮件的观察者
        WeatherObserver w1 = new WeatherObserver();
        w1.setName("天气邮件观察者");

        // 在天气变化后发短信的观察者
        WeatherObserver w2 = new WeatherObserver();
        w2.setName("天气短信观察者");

        // 城市天气数据
        WeatherData weatherData = new WeatherData("余杭");
        // 添加观察者
        weatherData.addObserver(w1);
        weatherData.addObserver(w2);

        // 气温变化
        weatherData.changeTemp("11:08", "32.8");
        // 气温变化
        weatherData.changeTemp("14:46", "29.3");
    }
}
```

必须先调用addObserver（）方法把观察者对象实例添加到被观察者实例中。

![](D:\编程日志\j5-4-5-1.svg)

观察者模式让观察者和被观察者双方的耦合度降到最低（称之为解耦）

# 第五章——并发编程

## 继承Thread类

### 实现类

```java
public class Person extends Thread {
    @Override
    public void run() {
        try {
            System.out.println(getName() + " 开始取钱");
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(getName() + " 取钱完毕");
    }
}
```

### 运行线程

```java
public class Bank {
    public static void main(String[] args) {
        Person thread1 = new Person();
        thread1.setName("张三");

        Person thread2 = new Person();
        thread2.setName("李四");

        thread1.start();
        thread2.start();
    }
}
```

Thread父类中有 `name`属性，所以可以调用 `setName（）`方法为线程设置名字，通过 `getName（）`就指代是哪个线程在运行

## 实现Runnable接口

```java
public class Person implements Runnable {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        try {
            System.out.println(name + " 开始取钱");
            Thread.sleep(200);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(name + " 取钱完毕");
    }
}
```

### 线程启动

```java
public class Bank {
    public static void main(String[] args) {
        Person person1 = new Person();
        person1.setName("张三");
        Thread thread1 = new Thread(person1);

        Person person2 = new Person();
        person2.setName("李四");
        Thread thread2 = new Thread(person2);

        thread1.start();
        thread2.start();
    }
}
```

## 线程安全

多个线程操作同一个资源的时候，发生了冲突的现象，就叫做线程不安全

```java
public class Ticket {
    public synchronized void sell() {
        count--;
        System.out.println(Thread.currentThread().getName() + "：卖出一张，还剩下 " + count + " 张票");
    }
}
```

利用 `synchronized`关键字来解决余量错乱的问题，也叫做同步锁，同一时刻只能由一个线程执行此方法

> 对于判断语句，最好在synchronized修饰的方法中写，在run里面写容易出现多个线程同时判断条件成立，就都会执行

## synchronized使用场景

使用 `synchronized`的方法满足了线程安全的两个特性

1. 原子性：方法全部执行且执行的过程不会被任何因素打断
2. 可见性：当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看到修改的值

缺点：滥用 `synchronized`会使得性能不高，多个线程需要等待

#### 比较适合的场景

1. 写操作的场景。例如用户修改个人信息、点赞、收藏、下单等
2. 尽量精确锁住最小的代码块，把最关键的写操作抽象成独立的方法加锁。

## 悲观锁和乐观锁

`AtomicInteger`提供了不使用 `synchronized`就能保证数据操作原子性的方法。例如 `decrementAndGet（）`方法

![](D:\编程日志\img\j5-5-5-2.png)

但仍有可能出现负数的情况。这是因为条件判断语句、操作语句、打印信息语句组合起来，就不具备原子性了，因为 `sell（）`不加锁，多条语句执行时就可能被其它线程打断了。

所以还得整体加 `synchronized`才能保证多条语句整体的原子性

### 乐观锁

乐观锁其实是不上锁，由于没有上锁，就提高了性能。

像 `AtomicInteger`类的 `incrementAndGet()`和 `decrementAndGet()`方法就是典型的乐观锁实现。

悲观锁

适合写数据比重更大的应用场景。一般来说写数据的整体消耗时间更长些，是可以接受的。

## 并发容器（一）

利用 `CompletableFuture`特性来解决。

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CompletableFuture;

public class StudentIDTest {
  public static void main(String[] args) {
    // 构建学生集合
    List<Student> studentList = new ArrayList<>();
    for (int i = 1; i <= 10; i++) {
      Student s = new Student();
      s.setName("学生" + i);
      studentList.add(s);
    }

    Register reg = new Register();

    studentList.forEach(s -> {
      CompletableFuture.supplyAsync(
          // 每个学生都注册学号
          () -> reg.regId(s)
        )
        // 学号注册完毕后，打印欢迎消息
        .thenAccept(student -> {
          System.out.println("你好 " + student.getName() + ", 欢迎来到春蕾中学大家庭");
        });
    });

    System.out.println("mission complate");
  }
}
```

`Stream parallelStream()`侧重点是流的元素的计算操作；而 `CompletableFuture`的任务可以比较宽泛。

## 并发容器（二）

```java
CompletableFuture.supplyAsync(() -> reg.regId(s))
  .thenApply(student -> {
    return dis.assignClasses(student);
  })
  .thenAccept(student -> {
     System.out.println("姓名：" + student.getName() + "，学号：" + student.getId() + "，班级号：" + student.getClassId());
  });
```

多步操作，可以调用多次thenApply()。由于末尾也要用到 `student`实例对象。所以位于中间的 `thenApply（）`方法，总是要 `return`学生实例对象，否则下一个步骤就获取不到了。

### 扩展知识点：返回值

`supplyAsync()` 是静态方法，返回值是 `CompletableFuture`实例对象。

```java
CompletableFuture<Student> cf = CompletableFuture.supplyAsync(() -> reg.regId(s))
  .thenApply(student -> {
    return dis.assignClasses(student);
  });
```

这个cf的类型是Student是因为return返回的是Student数据

### main（）方法的问题

可能线程任务还没执行完毕，`main（）`方法执行完毕，导致程序运行结束退出了。

```java
List<CompletableFuture> cfs = new ArrayList<>();
studentList.forEach(s -> {
  CompletableFuture<Void> cf = CompletableFuture.supplyAsync(() -> reg.regId(s))
    .thenApply(student -> {
        return dis.assignClasses(student);
    }).thenAccept(student -> {
        System.out.println("姓名：" + student.getName() + "，学号：" + student.getId() + "，班级号：" + student.getClassId());
    });

  cfs.add(cf);
});

try {
  // 等待所有的线程执行完毕
  CompletableFuture.allOf(cfs.toArray(new CompletableFuture[] {})).get();
} catch (Exception e) {
  e.printStackTrace();
}
```

将任务收集起来，然后等待所有的线程执行完毕。

类方法get（），作用就是等待所有的任务线程 `allOf（）收集的`都执行完毕，再继续执行。

#### 需要强调的是

在SpringBoot等服务端运行 `supplyAsync()`异步任务编排的时候，就没有必要可以使用 `get（）`方法等待所有线程任务执行完毕了。因为服务端往往是常驻程序，不像 `main（）`方法执行完毕就退出程序了。

### 再次理解同步与异步

`get（）`方法造成了 `main（）`方法等待，所以是同步的，通过`CompletableFuture`编排的任务，不会造成 `main（）`方法等待，是异步的。

### 安全的布尔值包装类

```java
AtomicBoolean ab = new AtomicBoolean(true);
boolean value = ab.get();
```

必须要有默认值

如果是多个值的话就得用{new AtomicBoolean(true)，}在里面写多个

## 线程池

线程池里面装满了线程，随用随取。线程可以被复用，一个线程可以执行A任务，也可以执行B任务，于是线程不再频繁创建和销毁。

> new Thread(register)意味着一个线程对象只能执行一个任务。线程池让线程与任务分离，不再紧密绑定

### 线程池创建

```java
import org.apache.commons.lang3.concurrent.BasicThreadFactory;

import java.util.concurrent.*;

public class StudentIDTest {

  // 线程工厂
  private static final ThreadFactory namedThreadFactory = new BasicThreadFactory.Builder()
    .namingPattern("studentReg-pool-%d")
    .daemon(true)
    .build();

  // 等待队列
  private static final BlockingQueue<Runnable> workQueue = new LinkedBlockingQueue<Runnable>(1024);

  // 线程池服务
  private static final ThreadPoolExecutor EXECUTOR_SERVICE = new ThreadPoolExecutor(
20,
200,
30,
TimeUnit.SECONDS,
workQueue,
namedThreadFactory,
new ThreadPoolExecutor.AbortPolicy()
      );

  public static void main(String[] args) {

  }
}
```

### BasicThreadFactory的pom依赖

```xml
<dependency>
  <groupId>org.apache.commons</groupId>
  <artifactId>commons-lang3</artifactId>
  <version>3.10</version>
</dependency>
```

### 创建线程工厂实例

```java
new BasicThreadFactory.Builder()
  .namingPattern("studentReg-pool-%d")
  .daemon(true)
  .build();
```

namingPattern（）定义线程名字的格式，可以把“studentReg”改掉

### 创建线程等待队列实例

new LinkedBlockingQueue<Runnable>(2048)

> 构建函数的参数表示能排队的任务个数

### 创建线程池实例

![image-20220827195950534](C:\Users\lyh\AppData\Roaming\Typora\typora-user-images\image-20220827195950534.png)

### 使用线程池执行任务

```java
public class StudentIDTest {
  public static void main(String[] args) {
    // 构建学生集合
    for (int i = 1; i <= 2000; i++) {
Student s = new Student();
s.setName("学生" + i);
Register register = new Register(s);
// 传入 Runnable 对象，运行任务
EXECUTOR_SERVICE.execute(register);
    }
  }
}
```

### 等待线程池执行

##### 问题

多线程程序还没有运行完，`main（）`函数就退出了，让main（）等待一段时间

```java
for (int i = 1; i <= 2000; i++) {
  ... ...
  ... ...
}

try {
    Thread.sleep(3000);
} catch (InterruptedException e) {
    e.printStackTrace();
}
```

## 线程池与并发容器

CompletableFuture内部也用到了线程池，实际上是把任务放入内部的**默认线程池**里执行

```java
CompletableFuture.supplyAsync(
    () -> reg.regId(s),
    EXECUTOR_SERVICE
  )
```

第二个参数传入的是构建好的线程池对象

线程池可以运行任务，但是不利于编排。

```
基础多线程
  ↓
线程池
  ↓
并发容器
```

一般学习阶段使用supplyAsync(() -> {})就够了。遇到任务并发度高的就需要指定线程池。或者一开始使用CompletableFuture默认线程池，任务执行慢的时候再开始考虑指定线程池，并调整线程池参数。
