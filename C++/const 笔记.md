# const 笔记
> 建议书写时将 `const` 放到类型后面从左往右读更加便于理解

## const
`const` 定义某个变量为`常量`。
> **注意**：**因为常量在定义之后就不能被修改，所以定义的时候必须初始化 **

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

## 指针
### 指向 const 对象的指针
C++ 中要求指向 `const` 对象的指针也必须具有 `const` 特性
```c++
	const double *cptr;
```

这里的 cptr 是指向 `double` 类型 `const` 对象的指针，**`const` 只是限定了 **cptr** 所指向的类型，而并非 **cptr** 本身**

- 在定义的时候不需要初始化
- 允许对 cptr 重新赋值，使其指向另一个 `const` 对象
- 一旦 cptr 被定义就**不能通过它**修改其所指对象的值

>**注意**：必须使用 `const void*` 类型的指针来保存 `const` 对象地址
>```c++
>const int universe = 42;
>const void *cpv = &universe;
>void *px = &universe;
>```

允许把非 `const` 对象的地址赋给指向 `const` 对象的指针

>**注意**：不能使用指向 `const` 对象的指针修改基础对象，然而如果该指针指向的是一个非 `const` 对象，可用其他方法修改其所指对象的值

### const 指针
`const` 指针：*本身的值不能被修改*

```c++
	int errNumb = 0;
	int *const curErr = &errNumb;
```

`const` 指针**必须初始化**

### 指向 const 对象的 const 指针
```c++
	int ptr = 110;
	const int *const pi_ptr = &ptr;
```

既不能修改 pi_ptr 所指对象的值，也不允许修改该指针的指向
从右往左阅读：“pi_ptr 首先是一个 `const` 指针，指向 `double` 类型的 `const` 对象”

### 指针与 typedef
```c++
	typedef string *pstring;
	const pstring p_str;
```

这里的 `const` 修饰的是 `pstring` ，实际上等价于
```c++
string *const p_str;
```

p_str 是一个指向 `string` 类型对象的 `const` 指针

## const 与函数
### const 形参
```c++
	void fcn(const int i);
```

尽管函数的形参是 `const`，但是编译器却将 fcn 中的形参视为普通的 `int` 型，目的是为了与 C 语言兼容，因为在 C 语言中，具有 `const` 形参或非 `const` 形参的函数无区别

### 利用 const 引用避免复制
如果使用引用形参的唯一目的是避免复制实参，则应该将形参定义为 `const` 引用

### 更加灵活的指向 const 的引用
应该将不修改相应的实参的形参定义为 `const` 引用。如果将这样的形参定义为非 `const` 引用，则毫无必要的限制了该函数的的使用。例如，可以编写下面的程序在一个 `string` 对象中查找一个指定的字符：
```c++
	string::size_tytpe find_char(string &s, char c) {
		string::size_type i = 0;
		while( i != s.szie() && s[i] != c)
			++i;
		return i;
	}
```

尽管函数没有修改这个形参的值，但是这样的定义带来的问题是不能通过字符串字面值来调用这个函数：
```c++
	if (find_char("hello",'e'))
```

这样会导致编译失败

所以应该将不需要修改的引用定义为 `const` 引用。普通的非 `const` 引用形参在使用的时候不太灵活。这样的形参既不能用 `const` 对象初始化，也不能用字面值或产生右值的引用的表达式实参初始化

