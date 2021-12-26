# Java8新特性

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









