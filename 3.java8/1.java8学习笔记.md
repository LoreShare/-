# Java8学习笔记

@author：loreshare

## 1.Lambda表达式初步与函数式接口

```java
1.什么是lambda表达式？
	用于表示匿名函数和闭包的运算符
    
2.为什么需要Lambda表达式？
    在Java中，我们无法将函数作为参数传递给一个方法，也无法声明返回一个函数的方法。
    
3.一个例子
    未使用Lambda之前
    public class SwingTest {
        public static void main(String[] args) {
            JFrame jFrame = new JFrame("My JFrame");
            JButton jButton = new JButton("My JButton");

            jButton.addActionListener(new ActionListener() {
                @Override
                public void actionPerformed(ActionEvent e) {
                    System.out.println("Button Pressed");
                }
            });

            jFrame.add(jButton);
            jFrame.pack();
            jFrame.setVisible(true);
            jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        }
    }

    使用Lambda之后
    public class SwingTest {
        public static void main(String[] args) {
            JFrame jFrame = new JFrame("My JFrame");
            JButton jButton = new JButton("My JButton");

            jButton.addActionListener(e -> System.out.println("Button Pressed"));

            jFrame.add(jButton);
            jFrame.pack();
            jFrame.setVisible(true);
            jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        }
	}

4.Lambda表达式的基本结构
    (param1,param2.param3) -> {
    
	}

5.一个例子
    public class Test1 {
        public static void main(String[] args) {
            List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9);

            //jdk1.4
            for (int i = 0; i < list.size(); i++) {
                System.out.println(list.get(i));
            }

            System.out.println("------------------------------------");

            //jdk1.5
            for (Integer integer : list) {
                System.out.println(integer);
            }

            System.out.println("------------------------------------");

            //jdk8
            list.forEach(new Consumer<Integer>() {
                @Override
                public void accept(Integer integer) {
                    System.out.println(integer);
                }
            });
        }
	}

	Consumer<T>接口（函数式接口）
    @FunctionalInterface
    public interface Consumer<T> {

        /**
         * Performs this operation on the given argument.
         *
         * @param t the input argument
         */
        void accept(T t);

        /**
         * Returns a composed {@code Consumer} that performs, in sequence, this
         * operation followed by the {@code after} operation. If performing either
         * operation throws an exception, it is relayed to the caller of the
         * composed operation.  If performing this operation throws an exception,
         * the {@code after} operation will not be performed.
         *
         * @param after the operation to perform after this operation
         * @return a composed {@code Consumer} that performs in sequence this
         * operation followed by the {@code after} operation
         * @throws NullPointerException if {@code after} is null
         */
        default Consumer<T> andThen(Consumer<? super T> after) {
            Objects.requireNonNull(after);
            return (T t) -> { accept(t); after.accept(t); };
        }
    }

	函数式接口注解
	**
     * An informative annotation type used to indicate that an interface
     * type declaration is intended to be a <i>functional interface</i> as
     * defined by the Java Language Specification.
     *
     * Conceptually, a functional interface has exactly one abstract
     * method.  Since {@linkplain java.lang.reflect.Method#isDefault()
     * default methods} have an implementation, they are not abstract.  If
     * an interface declares an abstract method overriding one of the
     * public methods of {@code java.lang.Object}, that also does
     * <em>not</em> count toward the interface's abstract method count
     * since any implementation of the interface will have an
     * implementation from {@code java.lang.Object} or elsewhere.
     *
     * <p>Note that instances of functional interfaces can be created with
     * lambda expressions, method references, or constructor references.
     *
     * <p>If a type is annotated with this annotation type, compilers are
     * required to generate an error message unless:
     *
     * <ul>
     * <li> The type is an interface type and not an annotation type, enum, or class.
     * <li> The annotated type satisfies the requirements of a functional interface.
     * </ul>
     *
     * <p>However, the compiler will treat any interface meeting the
     * definition of a functional interface as a functional interface
     * regardless of whether or not a {@code FunctionalInterface}
     * annotation is present on the interface declaration.
     *
     * @jls 4.3.2. The Class Object
     * @jls 9.8 Functional Interfaces
     * @jls 9.4.3 Interface Method Body
     * @since 1.8
     */
    @Documented
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.TYPE)
    public @interface FunctionalInterface {}

	翻译：
        	正如java语言规范的定义，这是一个声明性注释，用来表明一个接口是一个函数式接口。
        
        	从概念上看，一个函数式接口有且只有一个抽象方法。
        	因为java.lang.reflect.Method#isDefault()拥有实现，所以它并不是一个抽象方法。
          	如果一个接口声明了一个抽象方法，并且这个方法覆盖了java.lang.Object的public方法，这也不能给		这个接口的抽象方法数量+1，因为这个接口的任意一个实现都有来自java.lang.Object或其他地方的一个实		现。
        	注意，函数式接口的实例可以通过Lambda表达式，方法引用，构造方法引用来创建。
        	如果一个类型被打上函数式接口的注解，如果不满足条件的话，就会被编译器报错。
        	这个类型必须满足，这是一个接口，而不是一个注解，枚举或者类，并且这个被注解的类型必须满足函数式接		口的要求。
        	但是，编译器会将任意满足函数式接口定义的接口，视为函数式接口，不管它有没有打上注解。
        
    再看一个函数式接口
        @FunctionalInterface
        public interface Runnable {
            /**
             * When an object implementing interface <code>Runnable</code> is used
             * to create a thread, starting the thread causes the object's
             * <code>run</code> method to be called in that separately executing
             * thread.
             * <p>
             * The general contract of the method <code>run</code> is that it may
             * take any action whatsoever.
             *
             * @see     java.lang.Thread#run()
             */
            public abstract void run();
        }

6.关于函数式接口
    1.如果一个接口只有一个抽象方法，那么该接口就是一个函数式接口。
    	注：java8之后，接口中是可以有方法实现的，default和static
    2.如果我们在某个接口上声明了@FunctionalInterface注解，那么编译器就会按照函数式接口的定义来要求该接	口。
    3.如果某个接口只有一个抽象方法，但我们并没有给该接口声明@FunctionalInterface注解，编译器依旧会将该接	 口视为函数式接口。
```



## 2.深入函数式接口与方法引用

```java
@FunctionalInterface
interface MyInterface{
    /**
     * 抽象方法
     */
    void test();

    /**
     * 这个不算抽象方法，因为他有Object的默认实现
     * @return
     */
    String toString();
}

public class Test2 {

    public void myTest(MyInterface myInterface){
        System.out.println(1);
        myInterface.test();
        System.out.println(2);
    }

    public static void main(String[] args) {
        Test2 test2 = new Test2();
        /*
            1
            myTest
            2
        */
        test2.myTest(() -> {
            System.out.println("myTest");
        });

        System.out.println("-------------------------");

        MyInterface myInterface = () -> {
            System.out.println("hello");
        };
        //class com.loreshare.jdk8.day01.Test2$$Lambda$2/1078694789
        System.out.println(myInterface.getClass());
        //class java.lang.Object
        System.out.println(myInterface.getClass().getSuperclass());
        //interface com.loreshare.jdk8.day01.MyInterface
        System.out.println(myInterface.getClass().getInterfaces()[0]);
    }
}
```



## 3.Lambda表达式深入与流初步

```java
1.Lambda表达式作用
    在将函数作为一等公民的语言中，Lambda表达式的类型是函数
    但是
    在Java中，Lambda表达式是对象
    他们必须依附于一类特别的对象类型--函数式接口
    
2.上下文
    Lambda到底是什么类型的，要根据上下文来判定
    
    来看一个例子
interface TheInterface{
    void myMethod();
}

interface TheInterface2{
    void myMethod();
}

public class Test3 {

    public static void main(String[] args) {
        TheInterface theInterface1 = () -> {};
        //interface com.loreshare.jdk8.day01.TheInterface
        System.out.println(theInterface1.getClass().getInterfaces()[0]);

        TheInterface2 theInterface2 = () -> {};
        //interface com.loreshare.jdk8.day01.TheInterface2
        System.out.println(theInterface2.getClass().getInterfaces()[0]);
        
        /*
        实现线程的两种实现方式
                1.继承Thread类
                2.实现Runnable接口，然后将实现Runnable接口的实例作为Thread的一个构造方法的参数
                  在调用start方法，线程就启动了
        */
        //hello world
        new Thread(() -> {
            System.out.println("hello world");
        }).start();
        
        /*
        需求：
            List<String>里面都是小写的，把他变成大写
            并输出
            HELLO
            WORLD
            HELLO WORLD
        */
        List<String> list = Arrays.asList("hello","world","hello world");
        list.forEach(item -> {
            System.out.println(item.toUpperCase());
        });
        
        /*
        把转换后的数据放到新的集合里面
        */
        List<String> list2 = new ArrayList<>();//钻石语法、菱形语法(类型推断)
        list.forEach(item -> {
            list2.add(item.toUpperCase());
        });
        list2.forEach(System.out::println);

        /*
        stream流
            stream,串形流，整个程序有单线程完成
            parallelStream,并行流，是由多线程来完成的
        */
        list.stream().map(item -> item.toUpperCase())
                .forEach(item -> {
                    System.out.println(item);
                });
        //方法引用
        list.stream().map(String::toUpperCase).forEach(System.out::println);
        
    }
}    
```



## 4.Function接口详解



## 5.Function与BiFunction函数式接口详解



## 6.BiFunction函数式接口实例演示



## 7.Predicate函数式接口详解



## 8.Predicate深入剖析与函数式编程本质



## 9.Supplier与函数式接口总结



## 10.Optional深入详解



## 11.方法引用详解



## 12.方法引用场景剖析与默认方法分析



## 13.Stream介绍与操作方式详解



## 14.Stream深度解析与源码实践



## 15.Stream实例剖析



## 16.Stream陷阱剖析



## 17.内部迭代与外部迭代本质剖析及流本源分析



## 18.流的短路与并发流



## 19.Stream分组与分区详解



## 20.Collector源码分析与收集器核心



## 21.Collector同一性与结合性分析



## 22.Collector复合与注意事项



## 23.收集器用法详解与多级分组和分区



## 24.比较器详解与类型推断特例



## 25.比较器深入



## 26.自定义收集器实现



## 27.自定义收集器深度剖析与并行流陷阱



## 28.收集器枚举特性深度解析与并行流原理



## 29.Collectors工厂类源码分析与实战



## 30.groupingBy源码分析



## 31.partioningBy与groupingByConcurrent源码分析



## 32.Stream源码分析



## 33.Stream与BaseStream源码分析



## 34.分割迭代器源码剖析



## 35.分割迭代器与ForkJoin详解



## 36.分割迭代器实现分析



## 37.OfPrimitive与OfInt实现原理剖析



## 38.流源构造代码分析



## 39.ReferencePipeline与AbstractPipeline源码深度解读



## 40.IteratorSpliterator与流源操作方式详解



## 41.流调用机制与原理大揭秘



## 42.Sink与opWrapSink源码剖析



## 43.TerminalOp源码分析与终止操作层次体系



## 44.流延迟求值底层分析与Sink链接机制揭秘



## 45.Stream中间操作与终止操作层次体系分析与设计思想剖析



## 46.Joda项目介绍与实战



## 47.Java 8全新日期和时间API详解与UTC介绍



## 48.Java 8全新日期与时间API实战



## 49.Java 8深入剖析与实战课程总结与展望



