---
title: 六、Java循环控制
id: 62ebabac-0700-4585-a72d-be23d416e891
date: 2024-03-11 15:19:05
auther: tanc
cover: /upload/java.svg
excerpt: 九九乘法表 //九九乘法表public class MulFor01 {	//main	public static void main(String[] args){		int start = 1;		int end = 9;		for(int i = start; i <= end;
permalink: /archives/0db5c357-a132-45a3-8c78-7454f812cbc0
categories:
 - java
---

#### 九九乘法表

```java
//九九乘法表

public class MulFor01 {
	//main
	public static void main(String[] args){
		int start = 1;
		int end = 9;
		for(int i = start; i <= end; i++){
			for(int j = start; j<= i; j++){
				System.out.print(j + " * " + i + " = " + i*j +"\t");
			}
			System.out.println();
		}
	}
}
```



#### 

#### 计分程序

```java
import java.util.Scanner;


public class MulFor02 {
	//main
	public static void main(String[] agrs){
		//统计3个班成绩情况，每个班有5名同学，求出每个班的平均分和所有班级的平均分
		Scanner gradeScanner = new Scanner(System.in);
		int classNum = 3;
		int classStuNum = 5;
		int allClassSum = 0;
		int passNum = 0;
		double allClassAvg = 0;

		for (int i = 1; i<=classNum; i++){
			int sum = 0;
			double avg = 0;
			int grade = 0 ;
			System.out.println(i+"班成绩输入");
			for(int j = 1; j<= classStuNum; j++){
				System.out.println("请输入学号为"+j+"同学的成绩:");
				grade = gradeScanner.nextInt();
				sum += grade; //此班级总分
				//判断及格人数
				if(grade>=60){
					passNum++;
				}
			}

			allClassSum += sum; //所有班级总分
			avg = sum / classStuNum; //此班级的平均分
			System.out.println(i + "班的总成绩为: " + sum + "\n" + "平均成绩为: "+avg);
		}
		allClassAvg = allClassSum / (classNum * classStuNum);
		System.out.println("所有班级的平均成绩为: " + allClassAvg);
		System.out.println("所有班级及格人数有: " + passNum);
	}
}
```





#### 金字塔

```java
//打印金字塔
import java.util.Scanner;

public class Stars {
	//main
	public static void main(String[] args){
		int start = 1;
		Scanner starsScanner = new Scanner(System.in);
		System.out.print("请输入金字塔的层数:");
		int totalLevel = starsScanner.nextInt();
		int space  = totalLevel;
		//实心金字塔
		// for (int i = start ; i <= totalLevel; i++){
		// 	for (int x = 1; x < space; x++){
		// 			System.out.print(" ");
		// 	}
		// 	space--;
		// 	for (int j = start; j <= (2*i-1) ; j++ ) {
		// 		System.out.print("*");
		// 	}
		// 	System.out.println();
		// }

		//空心金字塔
		for (int i = start ; i <= totalLevel; i++){
			for (int x = 1; x < space; x++){
					System.out.print(" ");
			}
			space--;
			for (int j = start; j <= (2*i-1) ; j++ ) {
				//自己做的办法
				if(i == start || i == totalLevel){
					System.out.print("*");
				}else{
					System.out.print("*");
					for (int k = 1 ; k<= (2*i-1)-2 ; k++ ){
						System.out.print(" ");
					}
					System.out.print("*");
					break;
				}
				//老师演示的
				// if (j == 1 || j == (2*i-1) || i == totalLevel){
				// 	System.out.print("*");
				// }else{
				// 	System.out.print(" ");
				// }
			}
			System.out.println();
		}		
	}
}
```
