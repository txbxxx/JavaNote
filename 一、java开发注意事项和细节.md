---
title: 一、java开发注意事项和细节
id: ba5110f7-d476-4cad-b214-67c0bab7643d
date: 2024-01-26 17:24:50
auther: tanc
cover: /upload/java.svg
excerpt: 一、注意事项 java源文件以.java结尾，源文件的基本注册部分是类(class)，使用javac编译成.class文件，.class文件就是在以源文件中的类命名，如果有多个类，那么就会生成多个类文件 Java也是使用main函数作为程序入口，他又固定的写法如下面代码 //14点52分，2024年
permalink: /archives/61dff1ca-2436-41a5-bf86-aafe517ceb18
categories:
 - java
---

#### 一、注意事项

1. `java`源文件以`.java`结尾，源文件的基本注册部分是类(class)，使用`javac`编译成`.class`文件，`.class`文件就是在以源文件中的类命名，如果有多个类，那么就会生成多个类文件

2. Java也是使用main函数作为程序入口，他又固定的写法如下面代码

   ```java
   //14点52分，2024年1月22日
   
   //这是TanChang第一个java代码
   
   
   //public表示一个类class为公有
   public class Hello {
   	//主方法
   	public static void main(String[] args) {
   		System.out.println("hello,world！！"); //表示输出文字到屏幕, 而“；”表示语句的结束
   	}
   }
   ```

   

3.Java区分大小写

4.必须要以`";"`结尾

5.一个源文件中至多只有一个Public类，其他类型的类可以不限制，可以将main方法写在非Public类中，然后运行指定非Public类

```java
//14点52分，2024年1月22日

//这是TanChang第一个java代码


//public表示一个类class为公有
public class Hello {
	//主方法
	public static void main(String[] args) {
		System.out.println("hello,world！！"); //表示输出文字到屏幕, 而“；”表示语句的结束
	}
}





class Dog {

}


class cat {
	
}
```

编译后：

![image-20240122154531856](https://java-1305907375.cos.ap-nanjing.myqcloud.com/image-20240122154531856.png)

6.如果源文件中包含一个Public类，那么源文件就必须以那个Public类命名

下面是一个测试代码

![image-20240122155115354](https://java-1305907375.cos.ap-nanjing.myqcloud.com/image-20240122155115354.png)

#### 二、javadoc

需要配合文档注释来使用，看如下代码

```JAVA

/**
 * @author 谭畅
 * @version 1.0
*/

public class Doc_test {
	//程序入口
	public static void main(String[] args){
		System.out.println("test javadoc");
	}
}
```

如下就是文档注释，@后面的内容是不可以随便填写的

```
/**
 * @author 谭畅
 * @version 1.0
*/
```

配合javadoc命令使用

```
 javadoc -d G:\learn_code\JAVA\code\javadoc_test  -author -version .\Doc_test.java
 
 #这里的-auther -version 就是你的文档注释填写的内容，-d后面则是存储的位置
```

会在指定目录下生成，点击index.html文件

![image-20240122183933347](https://java-1305907375.cos.ap-nanjing.myqcloud.com/image-20240122183933347.png)

![image-20240122184008967](https://java-1305907375.cos.ap-nanjing.myqcloud.com/image-20240122184008967.png)