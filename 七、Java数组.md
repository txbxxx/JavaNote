---
title: 七、Java数组
id: ca22f06c-c6cd-4784-8b8b-edf4d1cc927e
date: 2024-03-11 15:19:05
auther: tanc
cover: /upload/java.svg
excerpt: 数组使用 动态初始化 		int[] a = new int[9];		a = {1,2,3,4,5,6,7,8,9}; 静态初始化 int[] a = {1,2,3,4,5,6,7,8,9}; 数组的扩容和缩减 //数组扩容import java.util.Scanner;pub
permalink: /archives/199a1e02-f1db-4dec-afe8-4647b0a25494
categories:
 - java
---

### 数组使用

#### 动态初始化

```java
		int[] a = new int[9];
		a = {1,2,3,4,5,6,7,8,9};
```

#### 静态初始化

```java
int[] a = {1,2,3,4,5,6,7,8,9};
```

#### 数组的扩容和缩减

```java


//数组扩容
import java.util.Scanner;


public class ArrayAdd{
	//main
	public static void main(String[] args){
		int addSize = 1;
		int[] arry1 = {1,2,3}; //定义初始数组
		//定义循环让用户可以继续添加
		while (true){
			int[] newArry = new int[arry1.length + addSize]; //定义扩容数组
			int addNum; //需要扩容的值

			Scanner addScanner = new Scanner(System.in);
			//将初始数组的值复制到扩容数组中
			for (int i = 0; i<arry1.length ; i++){
				newArry[i] = arry1[i];
			}
			//键盘输入需要添加的值
			System.out.print("请输入你想添加的值: ");
			addNum = addScanner.nextInt();
			//将输入的值加入到扩容模型的最后一个，应为最后一个数组就是为"0"
			newArry[newArry.length - 1] = addNum;

			//将初始数组指向扩容数组，初始数组的值就被销毁
			arry1 = newArry;

			for (int i = 0; i<arry1.length ; i++){
				System.out.print(arry1[i]+"\t");
			}
			System.out.println("\n是否继续添加?(y/n):");
			char judge = addScanner.next().charAt(0);
			if (judge == 'y'){
				continue;
			}else if (judge == 'n') {
				break;
			}else{
				System.out.println("输入错误");
				break;
			}
		}
	}
}
```

```java
//数组缩减
import java.util.Scanner;

public class ArrayReduce{
	//main
	public static void main(String[] args){
		int reduceSize = 1;
		int[] arry1 = {1,2,3,4,5};
		while (true) {
			int[] newArry = new int[arry1.length - reduceSize];
			Scanner reduceScanner = new Scanner(System.in);
			//将初始数组的值复制到扩容数组中
			for (int i = 0; i<newArry.length ; i++){
				newArry[i] = arry1[i];
			}
			arry1 = newArry;
			for (int i = 0; i<newArry.length ; i++){
				System.out.print(arry1[i]+"\t");
			}
			System.out.println("\n是否继续缩减?(y/n):");
			char judge = reduceScanner.next().charAt(0); //接受char类型
			if (judge == 'y'){
				if (arry1.length == 1 ){
					System.out.println("不能在缩减");
					break;
				}else {
					continue;
				}
			}else if (judge == 'n') {
				break;
			}else{
				System.out.println("输入错误");
				break;
			}
		}
	}
}
```

### ⚠️ 数组的使用和注意事项

- 数组是多个相同数据的组合，实现对这些数据的统一管理
- 数组中的元素可以是任何元素类型，包括基本类型和引用类型，但是不能混用
- 数组创建后，如果没有赋值，是有默认值的

  ```
  int 0	short 0		byte 0		long 0
  float 0.0	double 0.0		char \u0000
  boolen false	String null
  ```
- 使用数组的步骤为：1.声明数组并且开辟空间 2.给数组各个元素赋值 3.使用数组
- 数组属于引用类型，数组型数据是对象(object)

#### 二维数组

遍历二维数组中的值并求和

```java
//遍历二位数组并计算他们的和

public class TwoDimensionalArray01{
	//main
	public static void main(String[] args){
		int arr[][] = {{4,6},{1,4,5,7},{-2}};
		int sum = 0;
		for (int i = 0; i<arr.length; i++){
			for (int j = 0; j<arr[i].length; j++){
				sum += arr[i][j];
			}
		}
		System.out.print("二维数组的总数为:"+sum);
	}
}
```

杨辉三角

```java
//杨辉三角

public class YangHui {
	//main
	public static void main(String[] args){
		int arr[][] = new int[10][];
		for (int i = 0 ; i<arr.length; i++){
			arr[i] = new int[i];
			for (int j = 0 ; j<arr[i].length; j++){
				if (i <= 2){
					arr[i][j]=1;
				}else{
					if (j==0 || j == i-1){
						arr[i][j] = 1;
					}else{
						arr[i][j] = arr[i-1][j] + arr[i-1][j-1];
					}
				}
			}
		}
		for(int i = 0; i<arr.length; i++){
			for(int j=0; j<arr[i].length; j++){
				System.out.print(arr[i][j]+"\t");
			}
			System.out.println();
		} 
	}
}
```

⚠️二维数组的细节和注意事项

- 一维数组的声明方式有 `int[] x` 或者 `int x[]`
- 二维数组的声明方式有 `int[][] y` 、`int[] y[]`、`int y[][]`
- 二维数组是多个一维数组构成，，各个一维数组长度可以一样也可以不一样

#### 可变长参数

可变长参数就是可以传入多个对应类型的 `实参`，实现方法其实就是数组

```
public class Person {
    String name;
    int age;
    String sex;

    public void test(String... str){ //String... str 表示可以传递多个String类型的参数
         for (String s : str) { //加强For循环，使用这个就是可以遍历这个数组
            System.out.println(s);
        }
    }
}

```

可以直接传入多个参数，也可以不传参数

```
public class Main {

    public static void main(String[] args)  {
        Person per = new Person();
        per.test("1","2","3"); //直接传入
        Person per2 = new Person();
        per.test();

    }
}

//输出:
1
2
3
```

⭐️ 如果存在其他参数，则可变长参数必须是在后面

```
    public void test(int a,String... str){ //String... str 表示可以传递多个String类型的参数
        for (String s : str) {
            System.out.println(s);
        }
    }
```

⚠️ 这里只是拿String类型做个例子，可变长数组不仅限于String类型

```java
    public void test2(int... a){
        for (int i : a) {
            System.out.println(i);
        }
    }

    public void test2(long... a){
      
    }
```

之前一直狠好奇Main函数中的 `String[] args`是什么，它其实就是为我们在用命令行来执行 `JAVA`程序时所接受的参数，其实类似于 `shell`的 `$1，$2`

```java
    public static void main(String[] args)  {
        for (String arg : args) {
            System.out.println(arg); //本质上它也是一个数组，就可以使用遍历列表的方式来读取
        }
    }
```

如果时在代码编辑器中执行，输出应该是空，如果我们在命令行中使用后面在随便接入几个参数,我这里不知道为什么一直报错，如果正常应该就是会输出这后面接入的这几个参数

```
PS D:\learnCode\Java\src> java com/company/zy01/Main  bx bxb bx
错误: 找不到或无法加载主类 com.company.zy01.Main

```



#### 练习

```java
//已知有个升序的数组，要求插入个元素，该数组顺序依然是升序, 比如:[10，12，45，90]，添加23 后, 数组为 [10，12，23，45，90]
import java.util.Scanner;

public class Homework04 {
	//main
	public static void main(String[] args){
		//自己做的版本
		// int arr[] = {10,12,45,90};
		// int newArr[] = new int[arr.length+1];
		// Scanner addScanner = new Scanner(System.in);
		// for(int i = 0; i < newArr.length; i++){
		// 	if (i < arr.length){
		// 		newArr[i] = arr[i];
		// 	}else{
		// 		System.out.print("请输入你需要添加的值: ");
		// 		newArr[i] = addScanner.nextInt();
		// 	}
		// }

		// arr = newArr;

		// for(int i = 0; i<arr.length; i++){
		// 	for(int j = i+1; j<arr.length; j++){
		// 		if(arr[i] > arr[j]){
		// 			int temp = arr[i];
		// 			arr[i] = arr[j];
		// 			arr[j] = temp;
		// 		}
		// 	}
		// }

		// for (int i=0 ; i<arr.length ; i++ ) {
		// 	System.out.print(arr[i]+ " ");
		// }

		//老师的思路
		int arr[] = {10,12,45,90};
		int newArr[] = new int[arr.length+1];
		Scanner addScanner = new Scanner(System.in);
		System.out.print("请输入你需要添加的值: ");
		int addNum = addScanner.nextInt();
		int index = -1;
		for(int i = 0; i < arr.length; i++){
			if(arr[i] >= addNum){
				index = i;
				break;
			}
		}
		System.out.println(index);
		for (int i = 0,j=0 ; i< newArr.length ; i++){
			if (i != index){
				newArr[i] = arr[j];
				j++;
			}else{
				newArr[i] = addNum;
			}
		}

		arr = newArr;                                                                  

		for (int i=0 ; i<arr.length ; i++ ) {
			System.out.print(arr[i]+ " ");
		}

	}
}
```
