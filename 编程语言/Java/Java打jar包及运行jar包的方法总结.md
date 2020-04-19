java打jar包和运行jar包的两种方式
更详细的打包方式请参考https://www.cnblogs.com/mq0036/p/8566427.html

一、java类不依赖第三方jar包
以简单的一个java类Hello为例：
最终生成的jar包结构
 META-INF
    MENIFEST.MF
 Hello.class

方法步骤
（1）用记事本写一个Hello.java的文件
 class Hello{
     public static void main(String[] agrs){
         System.out.println("hello");
     }
 }
（2）用命令行进入到该目录下，编译这个文件
　　 javac Hello.java 
（3）将编译后的Hello.class文件打成jar包
　　 jar -cvf hello.jar Hello.class 
 　　c表示要创建一个新的jar包，v表示创建的过程中在控制台输出创建过程的一些信息，f表示给生成的jar包命名
（4）运行jar包
　　 java -jar hello.jar  这时会报错误：hello.jar中没有主清单属性 

　　 添加Main-Class属性
　　 用压缩软件打开hello.jar，会发现里面多了一个META-INF文件夹，里面有一个MENIFEST.MF的文件，用记事本打开
     Manifest-Version: 1.0
     Created-By: 1.8.0_121 (Oracle Corporation)

　 　在第三行的位置写入 Main-Class: Hello （注意冒号后面有一个空格，整个文件最后有一行空行），保存
 
　 　再次运行 java -jar hello.jar ，此时成功在控制台看到  hello ，成功。

二、java类依赖第三方jar包
假设类Hello依赖jar包Tom.jar，最终生成的jar包结构还是：
    META-INF
      MENIFEST.MF
    Hello.class

解释： Hello类与Tom.jar的调用关系如下：
class Hello{
    public static void main(String[] agrs){
         Tom.speak();
    }
}
class Tom{
    public static void speak(){
	  System.out.println("hello");
	}
}
Tom类已经打成了jar包Tom.jar

方法步骤：
（1）编写一个Hello.java并将其编译成Hello.class，注意，由于Hello里面引用了Tom类的speak方法，因此在打jar包时应使用-cp参数，将tom.jar包引入
　　 javac -cp tom.jar Hello.class 
  　 这里的 -cp 表示 -classpath，指的是把tom.jar加入classpath路径下
（2）将hello.class达成jar包
     jar -cvf hello.jar Hello.class
（3）引入jar包需要在MENIFEST.MF文件中配置一个新属性：Class-Path，路径指向你需要的所有jar包
　　 现在MENIFEST.MF这个文件应该变成：
     Manifest-Version: 1.0
     Created-By: 1.8.0_121 (Oracle Corporation)
     Main-Class: Hello
     Class-Path: Tom.jar
（4）使用java -cp Tom.jar;hello.jar hello来运行jar包