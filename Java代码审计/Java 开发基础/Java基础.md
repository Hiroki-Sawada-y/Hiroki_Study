## 语法规范
```Java
public class HelloWorld{ //类名： 1. 首字母要大写  2.  源文件名与类名相同
	
	// 单行注释

	/* 多行注释
		除这两个之外还有文档注释。不重要
		
	 * /
	public static void main (String[] args){  
		/* 
		1. main()⽅法是类体中的主⽅法，该⽅法从{开始到}结束。
		2. Java程序中的main()⽅法必须声明为：public static void ，
		3. 方法名应该小写字母开头
		*/
		
		System.out.println("Hello world");   // 输出hello world   每一行代码结束必须要有;
	}
}

```

![main方法](./media/main方法)

```bash
java  HelloWorld.java  // 编译
java HelloWorld // 运行
```
## 数据类型

### 变量定义
```java
变量声明： 
1. 数据类型 变量名 [= 值] ;
2. 多个变量：数据类型 变量名1 ,变量名2 ; 

int a ,b, c   ;
double d = 1.0 ;
```
> 常量 ：Java使用final关键字修饰常量   `final double PI =3.14；`
### 常见数据类型
1. 基本数据类型
	1. 数值型
		1. 整数类型 ：int byte short long
		2. 浮点类型 ： float double
	2. 字符型（char）
	3. 布尔型（boolean）
2. 引用数据类型
	1. 类 class
	2. 接口 interface
	3. 数组 String []
> String不是⼀个数据类型，⽽是引⽤数据类型，属于Java提供的⼀个类

### 多变量集合

## 控制流程

### 判断

### 循环

## 函数定义与使用

