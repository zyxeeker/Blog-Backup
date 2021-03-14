# const 笔记
## const
`const` 定义某个变量为`常量`。因为常量在定义之后就不能被修改，所以定义的时候必须初始化 

`const` 对象默认为**文件的局部变量**，非`const` 变量默认为`extern`。加了`extern`以后才能作为全局变量

### const 引用
```c++
	const int ival = 1024;
	const int &refval = ival;
```

只可以**读取** refval ，对他进行赋值和修改都是不合法的操作

### const 引用与非 const 引用的初始化区别
`const` 引用可以初始化为不同的类型的对象或者初始化为右值

```c++
    double d = 3.14;
    const int &ri = d;		// CORRECT

	int &ri_2 = d;			// ERROR
```

> 右值：左值可以出现在赋值语句的左边或右边
>  左值：只能出现在赋值语句的右边

非 `const` 引用只能绑定到与该引用类型相同的对象
`const` 引用则可以绑定到不同但相关的类型的对象或绑定到右值

