---
layout: default
title: JAVA序列化与反序列化
nav_order: 4
parent: JAVA
---


## java序列化与反序列化
### Serialization(序列化)：将java对象以一连串的字节保存在磁盘文件中的过程，也可以说是保存java对象状态的过程。序列化可以将数据永久保存在磁盘上(通常保存在文件中)。

### deserialization(反序列化)：将保存在磁盘文件中的java字节码重新转换成java对象称为反序列化。

- [原文链接](https://blog.csdn.net/qq_62414755/article/details/125886742)

## 序列化的应用
### 两个进程在远程通信时，可以发送多种数据，包括文本、图片、音频、视频等，这些数据都是以二进制序列的形式在网络上传输。

### java是面向对象的开发方式，一切都是java对象，想要在网络中传输java对象，可以使用序列化和反序列化去实现，发送发需要将java对象转换为字节序列，然后在网络上传送，接收方收到字符序列后，会通过反序列化将字节序列恢复成java对象。

java序列化的优点：
- 实现了数据的持久化，通过序列化可以把数据持久地保存在硬盘上(磁盘文件)。
- 利用序列化实现远程通信，在网络上传输字节序列。

## 序列化和反序列化地实现
### JDK类库提供的序列化API:  
-java.io.ObjectOutputStream    
  表示对象输出流，其中writeObject(Object obj)方法可以将给定参数的obj对象进行序列化，将转换的一连串的字节序列写到指定的目标输出流中。
-java.io.ObjectInputStream  
  该类表示对象输入流，该类下的readObject(Object obj)方法会从源输入流中读取字节序列，并将它反序列化为一个java对象并返回。

#### 序列化要求：
实现序列化的类对象必须实现了Serializable类或Externalizable类才能被序列化，否则会抛出异常。

### 实现java序列化和反序列化的三种方法：

现在要对student类进行序列化和反序列化，遵循以下方法：

方法一：若student类实现了serializable接口，则可以通过objectOutputstream和objectinputstream默认的序列化和反序列化方式，对非transient的实例变量进行序列化和反序列化。

方法二：若student类实现了serializable接口，并且定义了writeObject(objectOutputStream out)和

readObject(objectinputStream in)方法，则可以直接调用student类的两种方法进行序列化和反序列化。

方法三：若student类实现了Externalizable接口，则必须实现readExternal(Objectinput in)和writeExternal(Objectoutput out)方法进行序列化和反序列化。

### JDK类库中的序列化步骤：

第一步：创建一个输出流对象，它可以包装一个输出流对象，如：文件输出流。
````
ObjectOutputStream out = new ObjectOutputStream(new fileOutputStream("D:Student1.txt"));
````

第二步：通过输出流对象的writeObject()方法写对象
````
out.writeObject("hollo word");

out.writeObject("happy")

````
### JDK中反序列化操作：

第一步：创建文件输入流对象
````
ObjectInputStream in = new ObjectInputStream(new fileInputStream("E:\\JavaXuLiehua\\Student\\Student1.txt"));

````

第二步：调用readObject()方法
````
String obj1 = (String)in.readObject();

String obj2 = (String)in.readObject();

````

为了保证正确读取数据，对象输出流写入对象的顺序与对象输入流读取对象的顺序一致。
 
### 过程演示
- 创建一个student对象（继承Serializable）
````java
@Data
@AllArgsConstructor
public class Student implements Serializable {
    private String name;
    private String address;
    private int age;
}
````
- 创建一个main方法
````java
public class StudentMain {

    @SneakyThrows
    public static void main(String[] args) {
        Student student = new Student("小明","厦门",17);
        File file = new File("D:\\test.txt");
        if(file.exists()){
            System.out.println("该文件已存在，无需创建");
        }else {
            try {
                //创建对象
                file.createNewFile();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        try {
            //student类序列化
            FileOutputStream fileOutputStream = new FileOutputStream(file);
            //创建对象输出流
            ObjectOutputStream objO = new ObjectOutputStream(fileOutputStream);
            //写对象
            objO.writeObject(student);
            objO.flush();
            fileOutputStream.close();
            objO.close();
            
            //反序列化
            FileInputStream fis = new FileInputStream(file);
            //创建对象输入流
            ObjectInputStream ois = new ObjectInputStream(fis);
            //读取对象
            Student ss = (Student) ois.readObject();
            System.out.println(" 姓名："+ss.getName()+" 地址："+ss.getAddress()+" 年龄："+ss.getAge());
            ois.close();
            fis.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }

}
````
- 结果  
-- 控制台
````
该文件已存在，无需创建
姓名：小明地址：厦门年龄：17
````
-- txt文件
````
 sr com.ggdfm.demo2.entity.Student懽'VB€讘 I ageL addresst Ljava/lang/String;L nameq ~ xp   t 鍘﹂棬t 灏忔槑
````
#### transient关键字，表示有理的，被该关键字修饰则不能被序列化
````
private transient int age;//该成员不能被序列化
````
运行结果
````
该文件已存在，无需创建
姓名：小明 地址：厦门 年龄：0
````
此时可以看见，被transient关键字修饰的变量age并没有被序列化，返回了0。(修饰string类型则为空值)
### Externalizable接口实现序列化与反序列化
- Externalizable接口继承Serializable接口，实现Externalizable接口需要实现readExternal()方法和writeExternal()方法，
  这两个方法是抽象方法，对应的是serializable接口的readObject()方法和writeObject()方法，
  可以理解为把serializable的两个方法抽象出来。
  Externalizable没有serializable的限制，static和transient关键字修饰的属性也能进行序列化。

