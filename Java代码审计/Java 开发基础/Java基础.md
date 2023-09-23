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

## 变量和常量

### 声明变量
```java
变量声明： 
1. 数据类型 变量名 [= 值] ;
2. 多个变量：数据类型 变量名1 ,变量名2 ; 

int a ,b, c   ;
double d = 1.0 ;
```


### 声明常量
> 常量 ：Java使用final关键字修饰常量   `final double PI =3.14；`
常量一般用大写字母

### 变量类型
1. 成员变量：作用域为整个类，可被权限修饰符修饰
2. 局部变量：作用域为当前方法，不能被权限修饰符修饰
3. 静态变量：作用域为整个类，使用static修饰，可以被权限修饰符修饰，其值在运行期间只有一个副本
4. 参数变量：方法定义 变量，参数变量的作用域限于方法内部

```java
public class RunoobTest {
    // 成员变量
    private int instanceVar;
    // 静态变量
    private static int staticVar;
    
    public void method(int paramVar) {
        // 局部变量
        int localVar = 10;
        
        // 使用变量
        instanceVar = localVar;
        staticVar = paramVar;
        
        System.out.println("成员变量: " + instanceVar);
        System.out.println("静态变量: " + staticVar);
        System.out.println("参数变量: " + paramVar);
        System.out.println("局部变量: " + localVar);
    }
    
    public static void main(String[] args) {
        RunoobTest v = new RunoobTest();
        v.method(20);
    }
}
```

### 多变量集合

## 控制流程

### 判断

### 循环

## 函数定义与使用

