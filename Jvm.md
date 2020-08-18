## Jvm的优化

##### Jvm的参数

- 标准参数
  - -help
  - -version
- 非标准参数（-X）
  - -Xint
  - -Xcomp
- -XX参数（使用率较高）
  - -XX:newSize
  - -XX:UseSerialGc



JVM通过-D 来设置运行参数

Jvm的两种模式： Xint 和 Xcomp  解释模式和编译模式

Jvm中：XX:[+-]DisableExplicitGc  程序中是否禁用 System.gc()

-Xms512m:等价于  -XX:InitialHeapSize  设置堆内存的初始大小512M

-Xmx2048m:等价于  -XX:MaxHeapSize  设置堆内存的最大大小

-XX:+PrintFlagsFinal  会打印参数（值得操作符分为=和:=   意思是 默认值和被修改的值 ）

**jps**  查看java后台运行的进程

**jinfo -flags 进程ID**  查看一个进程的所有参数

**jstat**  命令查看内部堆内存各部分的使用情况，以及加载类的数量。

![jstat.jpg](C:\Users\18354\Desktop\学习的记录\引用的图片\jstat.jpg)

![compile](C:\Users\18354\Desktop\学习的记录\引用的图片\jstatcompile.jpg)

![jstatgc](C:\Users\18354\Desktop\学习的记录\引用的图片\jstatgc1.jpg)

![jstat](C:\Users\18354\Desktop\学习的记录\引用的图片\jstatgc.jpg)

### jmap

> jmap 比 jstat 可以获取更加详细的内容，内存的使用情况的汇总，对内存溢出的定位和分析。
>
> ![jmap1](C:\Users\18354\Desktop\学习的记录\引用的图片\jmap1.jpg)

>![jmap2](C:\Users\18354\Desktop\学习的记录\引用的图片\jmap2.jpg)
>
>![jmap](C:\Users\18354\Desktop\学习的记录\引用的图片\jmap3.jpg)

> ![jmap](C:\Users\18354\Desktop\学习的记录\引用的图片\jmap4.jpg)

> ![jmap5](C:\Users\18354\Desktop\学习的记录\引用的图片\jmap5.jpg)

> ![jhat](C:\Users\18354\Desktop\学习的记录\引用的图片\jhat.jpg)

> ![jhat](C:\Users\18354\Desktop\学习的记录\引用的图片\jhat2.jpg)

## JVM的内存模型

> * jdk1.7的内存模型
>
>   ![JDK1.7的内存模型](C:\Users\18354\Desktop\学习的记录\引用的图片\jdk7内存moxing.jpg)
>
>   Young（年轻代）：
>
>   ![edin.jpg](C:\Users\18354\Desktop\学习的记录\引用的图片\edin.jpg)
>
>   Tenured（老年代）：
>
>   ![tenured](C:\Users\18354\Desktop\学习的记录\引用的图片\tenured.jpg)
>
>   Perm（永久代）：
>
>   ![perm.jpg](C:\Users\18354\Desktop\学习的记录\引用的图片\perm.jpg)

>
>
>JDK1.8的内存模型
>
>​	![1.8的内存模型](C:\Users\18354\Desktop\学习的记录\引用的图片\jdk8的内存模型.jpg)
>
>![moxing](C:\Users\18354\Desktop\学习的记录\引用的图片\1.8内存.jpg)
>
>![metaspace.jps](C:\Users\18354\Desktop\学习的记录\引用的图片\metaspace.jpg)
>
>![perm.jpg](C:\Users\18354\Desktop\学习的记录\引用的图片\perm弃用.jpg)

## 内存溢出的定义和分析

> ![shizhan](C:\Users\18354\Desktop\学习的记录\引用的图片\内存溢出实战.jpg)

##  jastack

![jastack](C:\Users\18354\Desktop\学习的记录\引用的图片\jstack.jpg)

![xiancheng](C:\Users\18354\Desktop\学习的记录\引用的图片\线程.jpg)

![xianchgn2](C:\Users\18354\Desktop\学习的记录\引用的图片\线程2.jpg)

​														线程结束

##  死锁的问题

![visual](C:\Users\18354\Desktop\学习的记录\引用的图片\visual.jpg)