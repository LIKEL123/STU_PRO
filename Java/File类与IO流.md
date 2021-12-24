# File类与IO流

## Fie类

1、什么是File

```properties
File类是java.io包下代表与平台无关的文件和目录，也就是说如果希望在程序中操作文件和目录都可以通过File类来完成，
 File类能新建、删除、重命名文件和目录。
```
2、常用方法

```java
public class FileDemo1 {

		public static void main(String[] args) throws IOException {
			// 文件路径名
	//        String pathname = "D:\\aaa.txt";
	//        File file1 = new File(pathname);

			// 文件路径名
			String pathname2 = "E:\\0224\\bbb.txt";
			File file = new File(pathname2);
			//1 查看是否存在文件
			boolean exists = file.exists();
			//System.out.println(exists);

			//2 获取文件路径和文件名称
	//        System.out.println("文件构造路径:"+file.getPath());
	//        System.out.println("文件名称:"+file.getName());

			//3 public boolean isDirectory()` ：此File表示的是否为目录。
			//- `public boolean isFile()` ：此File表示的是否为文件。
	//        System.out.println(file.isDirectory());
	//        System.out.println(file.isFile());

			//相对路径
			//    ./  当前目录下
			//  ../   上层目录   ../../

			//- public boolean createNewFile()` ：当且仅当具有该名称的文件尚不存在时，创建一个新的空文件。
			//- `public boolean delete()` ：删除由此File表示的文件或目录。  只能删除空目录。
			//- `public boolean mkdir()` ：创建由此File表示的目录。单层目录
			//- `public boolean mkdirs()` ：创建由此File表示的目录，包括任何必需但不存在的父目录。 多层目录

			File  f = new File("E:\\aaa.txt");
	//        System.out.println(f.exists());
			boolean newFile = f.createNewFile();
	//        System.out.println(newFile);
	//        System.out.println(f.exists());

			File f1 = new File("E:\\test");
	//        System.out.println(f1.exists());
			boolean mkdir = f1.mkdir();
	//        System.out.println(mkdir);
	//        System.out.println(f1.exists());

			File f2 = new File("E:\\wwww\\abcd");
			boolean mkdir1 = f2.mkdir();
		   // System.out.println(mkdir1);

			boolean mkdirs = f2.mkdirs();
		   // System.out.println(mkdirs);

			boolean f1delete = f1.delete();
		   // System.out.println(f1delete);

			boolean f2delete = f2.delete();
		   // System.out.println(f2delete);

			File dir = new File("E:\\wwww");

			//获取当前目录下的文件以及文件夹的名称。
			String[] names = dir.list();
			for(String name : names){
				System.out.println(name);
			}
			//获取当前目录下的文件以及文件夹对象，只要拿到了文件对象，那么就可以获取更多信息
			File[] files = dir.listFiles();
			for (File file1 : files) {
				System.out.println(file1);
			}
		}
	}
```

3、递归操作

```java
import java.io.File;
import java.io.IOException;

public class FileDemo2 {

    public static void main(String[] args) throws IOException {
        File file = new File("E:\\wwww");
        listSubFiles(file);
    }

    //递归遍历所有目录
    public static void listSubFiles(File dir) {
        //判断
        if(dir !=null && dir.isDirectory()) {
            //获取dir里面内容
            File[] files = dir.listFiles();
            //files是否为空
            if(files != null) {
                //遍历
                for(File file:files) {
                    //递归调研
                    listSubFiles(file);
                }
            }
        }
        System.out.println(dir);
    }
}
```

## IO流

1、什么是IO

（1）针对文件内容操作，Java中I/O操作主要是指使用`java.io`包下的内容，进行输入、输出操作。输入也叫做读取数据，输出也叫做作写出数据。

2、IO的分类

根据数据的流向分为：**输入流**和**输出流**。
	- 输入流 ：把数据从`其他设备`上读取到`内存`中的流。 
	  - 以InputStream,Reader结尾
	- 输出流：把数据从`内存` 中写出到`其他设备`上的流。
	  - 以OutputStream、Writer结尾

根据数据的类型分为：**字节流**和**字符流**。
- **字节流** ：以字节为单位，读写数据的流。比如读取图片
  - 以InputStream和OutputStream结尾
- **字符流** ：以字符为单位，读写数据的流。比如读取文本
  - 以Reader和Writer结尾

3、字节流-输出流（重点）

（1）字节输出流  OutputStream 向文件写入内容

（2）OutPutStream子类 FileOutPutStream ， 专门针对文件操作 

（3）演示使用输出流写文件简单操作  代码

```java
    public static void main(String[] args) throws Exception {
        //创建操作文件输出流对象
        OutputStream outputStream = new FileOutputStream("E:\\0224\\atguigu.txt");
        //OutputStream outputStream = new FileOutputStream(new File("E:\\0224\\test.txt"));

        //向文件写入内容
        outputStream.write(97);
        outputStream.write(98);
        outputStream.write(99);

        //关闭输出流
        outputStream.close();
```

(4)字节数组写内容

```properties
    @Test
    public void outFile01() throws Exception {
        //创建操作文件输出流对象
        OutputStream out = new FileOutputStream("E:\\0224\\a.txt");
        //写内容
        byte[] b = "尚硅谷".getBytes();
        out.write(b);
        //关闭
        out.close();
    }
```

（5）指定位置字符写内容到文件

```java
    @Test
    public void outFile02() throws Exception {
        //创建操作文件输出流对象
        OutputStream out = new FileOutputStream("E:\\0224\\b.txt");
        //写内容
        byte[] b = "abcde".getBytes();
        //write 第一个参数开始位置 索引
        //      第二个参数 从开始位置取几个字符
        out.write(b,2,2);
        //关闭
        out.close();
    }
```

4、字节流 - 输入流（重点）

（1）字节输入流 InputStream ， 读文件， 常用方法：close   read

（2）InputStream子类 FileInputStream，专门进行文件读取操作

（3）调用read方法读取文件内容

```java
    public static void main(String[] args) throws Exception {
        //创建操作文件输入流对象
        InputStream inputStream = new FileInputStream("E:\\0224\\b.txt");
        //读取文件内容
        int read = inputStream.read();
        System.out.println((char) read);
        //关闭
        inputStream.close();
    }
```

（4）循环读取文件内容

```java
 @Test
    public void readFile01() throws Exception {
        //创建操作文件输入流对象
        InputStream inputStream = new FileInputStream("E:\\0224\\b.txt");
        //定义变量
        int b;
        //循环判断读取
        while((b = inputStream.read())!=-1) {
            System.out.println((char)b);
        }
        //关闭
        inputStream.close();
    }
```

（5）字节数组读取文件内容

```java
    @Test
    public void readFile02() throws Exception {
        //创建操作文件输入流对象
        InputStream inputStream = new FileInputStream("E:\\0224\\b.txt");
        //定义变量
        int len;
        //字节数组
        byte[] b = new byte[1024];
        //循环读取
        while((len = inputStream.read(b))!=-1) {
            System.out.println(new String(b));
        }
        //关闭
        inputStream.close();
```

5、字节输入流和输出流综合例子

```java
 public static void main(String[] args) throws Exception {
        //E:\01.jpg
        //1 创建输入流读取文件
        InputStream in = new FileInputStream("E:\\01.jpg");
        //2 创建输出流
        OutputStream out = new FileOutputStream("E:\\0224\\001.jpg");
        //3 流对接：把输入流内容放到输出流里面
        int len;
        byte[] b = new byte[1024];
        while((len=in.read(b))!=-1) {
            //把读到内容直接写到文件
            out.write(b,0,len);
        }
        //4 关闭
        out.close();
        in.close();
    }
```

6、字符流

（1）概述

当使用字节流读取文本文件的时候，可能会有哥问题，就是当文本遇到中文字符时，可能不会显示完整的字符，那是因为一个中文字符可能占用多个字节存储，所以Java提供一些字符流类，以字符为单位读写数据，专门用于处理文本文件。

（2）字符输入流 Reader    子类 FileReader

```java

```

7、其他流

（1）缓冲流

（2）转换流

（3）数据流

（4）序列化操作



