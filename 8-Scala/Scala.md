# Scala

## 1.变量

var  变量名: 变量类型 = 值      是可变变量

val  变量名 : 变量类型 = 值    是不可变变量

+ 如果类型可以通过变量值**推断**出来的话可以省略
+ 如果使用多态，那么类型将不能省略

## 2.标识符

Scala中的标识符可以用于声明运算符

不用记，错了改就行

## 3.字符串

字符串的拼接

   1.+    和Java中的字符串相加是一样的

2. 传值字符串  利用%s 占位符

   ```scala
   val name:String = "zhangsan"
   val passwd:String = "123123"
   
   printf("username":%s , name)
   printf("passwd":%s , passwd)
   ```

   3.插值字符串    在前面加个s   然后后面$符号 

   ```scala
   val name:String = "zhangsan"
   val passwd:String = "123123"   
   //插值字符串
   println(s"username:$name")
   ```

多行字符串  

+ 就是加三个"""abc"""  引号

```scala
val name:String = "zhangsan"
val passwd:String = "123123" 
    val  s =
      """
        | Hello
        | world
        |""".stripMargin
    println(s)

    val json =
      s"""
        |{"username":"$name","passwd":"$passwd"}
        |""".stripMargin

    print(json)
```



## 4.输入输出

1.输入

```scala
    // read - 控制台
    val str = StdIn.readLine()
    println(str)


    // 从文件中获取输入
    //数据源
    val source:BufferedSource = Source.fromFile("data/word.txt")
    val strings:Iterator[String] = source.getLines()
    while  (strings.hasNext){
      println(strings.next())
    }
```

2.输出

```scala
    val writer = new PrintWriter(new File("output/test.txt"))
    writer.write("Hello Scala")
    writer.close()

```



## 5.分布式计算

## 6.数据类型

Scala是完全面向对象的语言，所以不存在基本数据类型的概念，任意值对象类型（AnyVal）和任意引用对象类型(AnyRef)

AnyVal : 相当于Java中的基本数据类型   多了一个Unit  是无返回值类型 

AnyRef: Sring   Scala collections    all java classes    Other Scala classes

+ Null  是java classes的子类  Null    他是一个类型  只有一个对象就是null
+ Nothing  是Null的子类    就是在处理返回值类型的时候，统一处理正常返回和异常返回

 ![1644747108609](assets/1644747108609.png)

类型转换

```scala
自动类型转化（隐式转换）
object ScalaDataType {
    def main(args: Array[String]): Unit = {
        val b : Byte = 10
        val s : Short = b
        val i : Int = s
        val lon : Long = i
}
}


强制类型转化

var a : Int = 10
Var b : Byte = a.toByte
// 基本上Scala的AnyVal类型之间都提供了相应转换的方法。 
```

## 7.运算符

scala中的语法双等号就是比较对象的内容，但是和equals不完全一样     前置进行非空判断

 user.tostring()   可以携程user tostring()  就是万物皆对象，   点可以省略

## 8.流程控制

单分支      if else  java一样

多分支      if   else    if      和java也一样

```scala
// todo 表达式的返回结果为：表达中满足条件的最后一行代码 的执行结果

    val age = 30
    // todo 表达式的返回结果为：表达中满足条件的最后一行代码 的执行结果
    val result = if  (age == 30) {
      println("30")
      "zhangsan"
    }


    println(result)


-->       "zhangsan"

```

for 循环

for ( 循环变量 <- 数据集 ) {

​    循环体

}

```scala
for (元素 : 元素类型  <-  1 to 5   把每个元素遍历出来)
for (i : Int <- 1 to 5){
      println(i)
    }


元素类型Int  可以省略
val range = 1 to 5
for (i <- range){
    println(i)
}
```

```scala
object ScalaLoop {
    def main(args: Array[String]): Unit = {
        for ( i <- Range(1,5) ) { // 范围集合
            println("i = " + i )
        }
        for ( i <- 1 to 5 ) { // 包含5
            println("i = " + i )
        }
        for ( i <- 1 until 5 ) { // 不包含5
            println("i = " + i )
        }
    }
}
```

**2)** **循环守卫**

```scala
循环时可以增加条件来决定是否继续循环体的执行,这里的判断条件我们称之为循环守卫

object ScalaLoop {

​    def main(args: Array[String]): Unit = {

​        for ( i <- Range(1,5) if i != 3  ) {

​            println("i = " + i )

​        }

​    }

}
```

​	**3)** **循环步长**

```scala
scala的集合也可以设定循环的增长幅度，也就是所谓的步长step
object ScalaLoop {
    def main(args: Array[String]): Unit = {
        for ( i <- Range(1,5,2) ) {
            println("i = " + i )
        }
        for ( i <- 1 to 5 by 2 ) {
            println("i = " + i )
        }
    }
}
```

4)**循环嵌套**

```scala
object ScalaLoop {
    def main(args: Array[String]): Unit = {
        for ( i <- Range(1,5); j <- Range(1,4) ) {
            println("i = " + i + ",j = " + j )
        }
        for ( i <- Range(1,5) ) {
            for ( j <- Range(1,4) ) {
                println("i = " + i + ",j = " + j )
            }
        }
    }
}
```

5) **引入变量**

```scala
object ScalaLoop {
    def main(args: Array[String]): Unit = {
        for ( i <- Range(1,5); j = i - 1 ) {
            println("j = " + j )
        }
    }
}
```

**6)** **循环****返回值**

```scala
scala所有的表达式都是有返回值的。但是这里的返回值并不一定都是有值的哟。

如果希望for循环表达式的返回值有具体的值，需要使用关键字yield

object ScalaLoop {

​    def main(args: Array[String]): Unit = {

​        val result = for ( i <- Range(1,5) ) yield {

​            i * 2

​        }

​        println(result)

​    }

}
```

while循环

和Java的while循环一样



break 循环中断

```scala
scala是完全面向对象的语言，所以无法使用break，continue关键字这样的方式来中断，或继续循环逻辑，而是采用了函数式编程的方式代替了循环语法中的break和continue

object ScalaLoop {

​    def main(args: Array[String]): Unit = {

​        scala.util.control.Breaks.breakable {

​            for ( i <- 1 to 5 ) {

​                if ( i == 3 ) {

​                    scala.util.control.Breaks.break

​                }

​                println(i)

​            }

​        }

​    }

}
```



## 9.函数式编程

 函数式编程

将问题分解成一个一个的步骤，将每个步骤进行封装（函数），通过调用这些封装好的功能按照指定的步骤，解决问题。

```scala
[修饰符] def 函数名 ( 参数列表 ) [:返回值类型] = {
    函数体
}

private def test( s : String ) : Unit = {
    println(s)
}
```

类中声明的函数称之为方法，其他场合声明的就是函数了

**函数的声明和调用**

```scala
object Scala02_Function_Def {

  def main(args: Array[String]): Unit = {
    // TODO 函数时编程
    //TODO 1. 无参，无返回值
    def fun1(): Unit ={
      println("fun1...")
    }
    fun1()
    //如果函数声明时。没有参数，那么在调用的时候可以省略小括号
    fun1
    //TODO 2. 无参，有返回值
    def fun2(): String ={
      return "zhangsan"
    }
    val s = fun2
    println(s)
    //TODO 3. 有参，无返回值
    def fun3(name : String): Unit ={
      val s = s"name:" +{name}
      println(s)
    }

    fun3("xiaoli")
    //TODO 4. 有参，有返回值
    def fun4(name : String): String ={
      return "Name : " + name
    }

    val xiaodong = fun4("xiaodong")
    println(xiaodong)

    //TODO 5. 多参，无返回值
    def fun5(name:String,age:Int): Unit ={
      println(s"Nmae: ${name}, Age : ${age}")
    }


    fun5("dongchao",18)
    //TODO 6. 有参，有返回值
    def fun6(name:String,age:Int): String ={
     return s"Nmae: ${name}, Age : ${age}"
    }

    val lili = fun6("lili",15)
    print(lili )
  }
}
 
```

```scala
1)可变参数
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        def fun7(names:String*): Unit = {
            println(names)
        }
        fun7()
        fun7( "zhangsan" )
        fun7( "zhangsan", "lisi" )
    }
}
可变参数不能放置在参数列表的前面，一般放置在参数列表的最后
oobject ScalaFunction {
    def main(args: Array[String]): Unit = {
        // Error
        //def fun77(names:String*, name:String): Unit = {
            
        //}
        def fun777( name:String, names:String* ): Unit = {
            println( name )
            println( names )
        }
    }
}
2)参数默认值
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        def fun8( name:String, password:String = "000000" ): Unit = {
            println( name + "," + password )
        }
        fun8("zhangsan", "123123")
        fun8("zhangsan")
    }
}
3)带名参数
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        def fun9( password:String = "000000", name:String ): Unit = {
            println( name + "," + password )
        }
        fun9("123123", "zhangsan" )
        fun9(name="zhangsan")
    }
}
```

**函数至简原则**

```scala
1)省略return关键字
// 函数体会将满足条件最后一行代码的执行结果作为函数的返回值 
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        def fun1(): String = {
            return "zhangsan"
        }
        def fun11(): String = {
            "zhangsan"
        }
    }
}
```

```scala
2)省略返回值类型
// 如果 函数返回数据， 那么可以推断出返回值类型的华，返回值类型就可以省略 
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        def fun3() = {
            "zhangsan"
                     }
    }
}
```

```scala
3)省略花括号
// 如果函数体的逻辑代码只有一行的时候，  那么大括号可以省略
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        def fun2()=  "zhangsan"    }
}
```



```scala
4)省略等号
如果函数体中有明确的return语句，那么返回值类型不能省略
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        def fun5(): String = {
            return "zhangsan"
        }
        println(fun5())
    }
}
如果函数体返回值类型明确为Unit, 那么函数体中即使有return关键字也不起作用 
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        def fun5(): Unit = {
            return "zhangsan"
        }
        println(fun5())
    }
}
如果函数体返回值类型声明为Unit, 但是又想省略，那么此时就必须连同等号一起省略
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        def fun5() {
            return "zhangsan"
        }
        println(fun5())
    }
}
```



```scala
5)省略参数列表
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        def fun4 = "zhangsan"
        fun4// OK
        fun4()//(ERROR)
    }
}
```

```scala
6)省略名称和关键字
object ScalaFunction {
    def main(args: Array[String]): Unit = {
        // 匿名函数
        () => {
            println("zhangsan")
        }
    }
}
```

### 下划线的作用

1.声明变量， 但是不能访问

​	val _ = "zhangsan"

2. 将函数作为整体使用

   如果将函数作为整体去使用，而不是执行结果赋值给变量，那么需要采用特殊符号:   下划线 

   val f2 = fun2 _ 

3. 如果变量声明的类型为函数类型，那么可以不适用下划线来让函数作为对象

   函数的另外一种写法：  ( 输入参数类型 ) => 输出类型

   ```scala
   def test1 (name:String,age : Int) : String = {
       "123"
   }
   val ff:(String,Int) =>String = test1
   ```

   函数作为值

   ```scala
   object ScalaFunction {
       def main(args: Array[String]): Unit = {
           def fun1(): String = {
               "zhangsan"
           }
           val a = fun1
           val b = fun1 _
           val c : ()=>Unit = fun1
           println(a)
           println(b)
       }
   }
   ```

   ### **下划线省略到最简**

    匿名函数在使用的时候也可以遵循至简原则

   //1. 如果函数体的逻辑代码只有一行，大括号可以省略， 代码卸载一行中

   // 2. 如果参数的类型可以推断出来，那么参数类型可以省略  

    // 3. 如果参数只有一个的话，参数列表的小括号可以省略
    // 4. 如果参数在使用时，按照顺序只使用了一次，那么可以使用下划线代替参数，

   ```scala
   
   object Scala06_Function_Hell_1 {
   
       def main(args: Array[String]): Unit = {
   
           // TODO 函数式编程 - 地狱版
           /*
               public void test( User user ) {
               public void test( 函数类型 参数名称 ) {
   
               }
   
            */
           // TODO 2. 将函数作为参数来使用
   //        def test(  f : ()=>Unit ): Unit = {
   //            f()
   //        }
   //
   //        // 函数对象
   //        def fun1(): Unit = {
   //            println("xxxxxxx")
   //        }
   //
   //        val ff = fun1 _
   //
   //        test( ff )
           def test( f : (Int, Int) => Int ): Unit = {
               val r = f(10, 20)
               println(r)
           }
   //
   //        def fun2(x:Int, y:Int): Int = {
   //            x + y
   //        }
   //        def fun3(x:Int, y:Int): Int = {
   //            x - y
   //        }
   //        def fun4(x:Int, y:Int): Int = {
   //            x * y
   //        }
   //
   //        def fun5(x:Int, y:Int): Int = {
   //            x + y
   //        }
   
           //test(fun2)
           //test(fun3)
           //test(fun4)
   
           // TODO 匿名函数主要应用于函数作为参数使用
   //        test(
   //            (x:Int, y:Int) => {
   //                x - y
   //            }
   //        )
           // TODO 匿名函数在使用时也可以存在至简原则
           // 1. 如果函数体的逻辑代码只有一行，大括号可以省略，代码写在一行中
   //        test(
   //            (x:Int, y:Int) => x - y
   //        )
           // 2. 如果参数的类型可以推断出来，那么参数类型可以省略的
   //        test(
   //            (x, y) => x - y
   //        )
           // 3. 如果参数只有一个的话，参数列表的小括号可以省略
           // 4. 如果参数在使用时，按照顺序只使用了一次，那么可以使用下划线代替参数，
           //test(_ * _)
       }
   }
   ```

   ### 函数作为返回值使用

```scala

object Scala06_Function_Hell_6 {

    def main(args: Array[String]): Unit = {

        // TODO 函数式编程 - 地狱版
        // TODO 3. 将函数作为返回值返回
        /*
           public User getUser() {
              return new User();
           }
         */
        def test(): Unit = {
            println("function...")
        }

        def fun() = {
            test _
        }

        //val f = fun _
        //val ff = f()
        //ff()

        fun()()

        test()

    }
}

```

