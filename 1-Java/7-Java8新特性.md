# Java8新特性   .2021.12.29

## Lambda表达式

1、Lambda使用函数式编程思想，最大的特点：关注结果，不关注过程

2、使用lambda表达式可以简化代码的编写

```properties
第一步: 复制小括号
第二步: 写死右箭头
第三步: 落地大括号(可有可无)
```

3、lambda使用特点

（1）lambda表达式主要简化接口的部分代码的，接口里面必须只能有一个普通方法

（2）把只有一个普通方法的接口，称为**函数式接口**，在函数式接口上面有一个标志，在接口上面有注解 **@FunctionalInterface，函数式接口可以有default和static方法**

4、自定义函数式接口

```java
public class Demo2 {

    public static void main(String[] args) {
        //实现Foo接口add方法
        Foo foo = (int a,int b) -> {
            System.out.println("lambda test...");
            return a+b;
        };

        //调用
        int add = foo.add(2, 3);
        System.out.println(add);

        int div = foo.div(4, 2);
        int sub = Foo.sub(3, 1);
        System.out.println(div);
        System.out.println(sub);
    }
}

//创建函数式接口
@FunctionalInterface
interface Foo {
    //两个数相加
    public int add(int a,int b);

    //其他类型的方法
    default int div(int x,int y) {
        return x/y;
    }

    public static int sub(int x,int y) {
        return x-y;
    }
}
```



![1640790250363](assets/1640790250363.png)

```java
（1）消费型接口
/**
 * 演示函数式接口
 */
public class Demo4 {
    //消费型
    @Test
    public void test01() {
        Consumer<String> consumer = (t) -> {
            System.out.println(t);
        };
        consumer.accept("mary");
    }

    @Test
    public void test02() {
       //创建list集合，向放数据，把list集合遍历
        List<String> list = Arrays.asList("java","c","python","c++","VB","C#");
        list.forEach( s-> { System.out.println(s); });
    }

    @Test
    public void test03() {
        Map<Integer, String> map = new HashMap<>();
        map.put(1, "java");
        map.put(2, "c");
        map.put(3, "python");
        map.put(4, "c++");
        map.put(5, "VB");
        map.put(6, "C#");
        map.forEach( (k,v)-> { System.out.println(k+"::"+v); });
    }
}
 
（2）供给型接口
//供给型接口
@Test
public void test04() {
    Supplier<String> supplier = () -> {
        return "atguigu";
    };
    String value = supplier.get();
    System.out.println(value);
}

（3）函数型（功能型）接口
//函数型接口
@Test
public void test05() {
    Function<String,String> function = (t) -> {
        System.out.println(t);
        return "atguigu";
    };
    String value = function.apply("lucymary");
    System.out.println(value);
}

public class Demo5 {

    public static void main(String[] args) {
        //创建map集合，放数据 key 编号  value对象
        Map<Integer,Employee> map = new HashMap<>();
        map.put(1,new Employee(1,"lucy",8000));
        map.put(2, new Employee(2, "李四", 9000));
        map.put(3, new Employee(3, "王五", 10000));

        //遍历map集合
        map.forEach((k,v)-> {
            System.out.println("key:"+k+"  value:"+v);
        });
        
        //把map工资，如果工资小于10000，替换为10000
        map.replaceAll((k,v)->{
            //从v对象获取工资值，判断如果小于10000
            if(v.getSalary()<10000) {
            
                         v.setSalary(10000);
            }
            return v;
        });
        map.forEach((k,v) -> System.out.println(k+"="+v));
    }
}

class Employee{
    private int id;
    private String name;
    private double salary;
    public Employee(int id, String name, double salary) {
        super();
        this.id = id;
        this.name = name;
        this.salary = salary;
    }
    public Employee() {
        super();
    }
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public double getSalary() {
        return salary;
    }
    public void setSalary(double salary) {
        this.salary = salary;
    }
    @Override
    public String toString() {
        return "Employee [id=" + id + ", name=" + name + ", salary=" + salary + "]";
    }

}

（4）断定型接口
//断定型接口
@Test
public void test01() {
    ArrayList<String> list = new ArrayList<>();
    list.add("hello");
    list.add("java");
    list.add("atguigu");
    list.add("ok");
    list.add("yes");

    //遍历list集合
    list.forEach(str->{ System.out.println(str); });
    System.out.println();
    //如果字符串长度小于5，删除这个字符串
    list.removeIf(str->str.length()<5);

    list.forEach(str->System.out.println(str));
```



## **Stream流**

**1、什么是Stream流**

Stream是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列。“集合讲的是数据，负责存储数据，Stream流讲的是计算，负责处理数据！

**3、使用Stream步骤**

**Stream 的操作三个步骤：**

1- 创建 Stream：通过一个数据源（如：集合、数组），获取一个流

2- 中间操作：中间操作是个操作链，对数据源的数据进行n次处理，但是在终结操作前，并不会真正执行。

3- 终止操作：一旦执行终止操作，就执行中间操作链，最终产生结果并结束Stream。

**4、创建Stream流**

```java
/**
 * 创建Stream流
 */
public class StreamDemo1 {
    public static void main(String[] args) {
        //1 通过集合创建
        List<Integer> list = Arrays.asList(1,2,3,4,5);
        Stream<Integer> stream1 = list.stream();

        //2 通过数组
        int[] arr = {1,2,3,4,5};
        IntStream stream2 = Arrays.stream(arr);

        //3 Stream里面of方法
        Stream<Integer> stream3 = Stream.of(1,2,3,4,5);

        //4 创建无限流
        Stream<Double> stream4 = Stream.generate(Math::random);
        Stream<Integer> stream5 = Stream.iterate(1, num -> num+=2);
    }
}
```

**5、中间操作**

```java
/**
 * 创建Stream流
 */
public class StreamDemo1 {
    public static void main(String[] args) {
        //1 通过集合创建
        List<Integer> list = Arrays.asList(1,2,3,4,5);
        Stream<Integer> stream1 = list.stream();

        //2 通过数组
        int[] arr = {1,2,3,4,5};
        IntStream stream2 = Arrays.stream(arr);

        //3 Stream里面of方法
        Stream<Integer> stream3 = Stream.of(1,2,3,4,5);

        //4 创建无限流
        Stream<Double> stream4 = Stream.generate(Math::random);
        Stream<Integer> stream5 = Stream.iterate(1, num -> num+=2);
    }

    //中间操作 filter
    @Test
    public void test01() {
        //创建Stream流
        Stream<Integer> stream = Stream.of(1,2,3,4,5,6);
        //中间操作
        stream = stream.filter(t -> t % 2 == 0);
        //终止操作，输出
        stream.forEach(System.out::println);
    }

    @Test
    public void test02(){
        Stream.of(1,2,3,4,5,6)
                .filter(t -> t%2==0)
                .forEach(System.out::println);
    }

    @Test
    public void test03(){
        Stream.of(1,2,3,4,5,6,2,2,3,3,4,4,5)
                .distinct()
                .forEach(System.out::println);
    }

    @Test
    public void test06(){
        Stream.of(1,2,3,4,5,6,2,2,3,3,4,4,5)
                .skip(5)
                .forEach(System.out::println);
    }

    @Test
    public void test05(){
        Stream.of(1,2,2,3,3,4,4,5,2,3,4,5,6,7)
                .distinct()  //(1,2,3,4,5,6,7)
                .filter(t -> t%2!=0) //(1,3,5,7)
                .limit(3)
                .forEach(System.out::println);
    }


    @Test
    public void test04(){
        Stream.of(1,2,3,4,5,6,2,2,3,3,4,4,5)
                .limit(4)
                .forEach(System.out::println);
    }

    @Test
    public void test11(){
        String[] arr = {"hello","world","java"};
        Arrays.stream(arr)
                .map(t->t.toUpperCase())
                .forEach(System.out::println);
    }
}
 
6、终止操作
public class StreamDemo2 {

    @Test
    public void test14(){
        List<Integer> list = Stream.of(1,2,4,5,7,8)
                .filter(t -> t%2==0)
                .collect(Collectors.toList());

        System.out.println(list);
    }


    @Test
    public void test13(){
        Optional<Integer> max = Stream.of(1,2,4,5,7,8)
                .reduce((t1,t2) -> t1>t2?t1:t2);//BinaryOperator接口   T apply(T t1, T t2)
        System.out.println(max);
    }

    @Test
    public void test12(){
        Integer reduce = Stream.of(1,2,4,5,7,8)
                .reduce(0, (t1,t2) -> t1+t2);//BinaryOperator接口   T apply(T t1, T t2)
        System.out.println(reduce);
    }

    @Test
    public void test11(){
        Optional<Integer> max = Stream.of(1,2,4,5,7,8)
                .max((t1,t2) -> Integer.compare(t1, t2));
        System.out.println(max.get());
    }

    @Test
    public void test10(){
        Optional<Integer> opt = Stream.of(1,2,4,5,7,8)
                .filter(t -> t%3==0)
                .findFirst();
        System.out.println(opt);
    }

    @Test
    public void test09(){
        Optional<Integer> opt = Stream.of(1,2,3,4,5,7,9)
                .filter(t -> t%3==0)
                .findFirst();
        System.out.println(opt.get());
    }

    @Test
    public void test08(){
        Optional<Integer> opt = Stream.of(1,3,5,7,9).findFirst();
        System.out.println(opt.get());
    }

    @Test
    public void test04(){
        boolean result = Stream.of(1,3,4,7,9)
                .anyMatch(t -> t%2==0);
        System.out.println(result);
    }


    @Test
    public void test03(){
        boolean result = Stream.of(1,3,5,7,9)
                .allMatch(t -> t%2!=0);
        System.out.println(result);
    }

    @Test
    public void test02(){
        long count = Stream.of(1,2,3,4,5)
                .count();
        System.out.println("count = " + count);
    }
}
```



