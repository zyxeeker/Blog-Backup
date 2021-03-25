## Notes
### 传递指向指针的引用
示例：
- 交换函数 ptrswap：
```c++
void ptrswap(int *&i, int *&j){
	int *tmp = v2;
	v2 = v1;
	v1 = tmp;
}
```

- 主函数：
```c++
int main(){
	int i = 10;
	int j = 20;
	int *pi = &i;
	iny *pj = &j;
	ptrswap(pi, pj);
}
```

### 通过引用传递数组
```c++
void printValues(int (&arr)[10]) {...}
```

>**注意**：*&arr* 两边的括号是必须的，因为下标操作具有更高的优先级

### 多维数组的传递
两种形式：
```c++
// 括号必须存在
void printValues(int (martix*)[10]) {...}

void printValues(int martix[][10]) {...}
```

### 防止操作导致数组越界
有三种方式防止数组越界：
#### 1. 使用标准库规范
传递指向**数组第一个**和**最后一个元素下一个位置的指针**。（相当于 `vector` 的 `begin` 和 `end`）
```c++
void printValues(const int *beg, const int *end) {...}
```

#### 2.显示传递表示数组大小的形参
将数组的大小作为形参传递到函数之中

#### 3. 放置标记
类似于 C 风格字符串

### 关于 main
#### 处理命令行选项
```c++
int main(int argc, char *argv[]) {...}
```

或者
```c++
int main(int argc, char **argv) {...}
```

*argc* 用于传递该数组中字符串的个数，*argc* 是一个 C 风格字符串数组

假设我们主函数位于名为 *prog* 的可执行文件中，可将如下实参选项传递给程序：
```
prog -d -o ofile data0
```

*main* 中获得数据如下：
```c++
argv[0] = "prog";
argv[1] = "-d";
argv[2] = "-o"
argv[3] = "ofile";
argv[4] = "data0";
```

### 返回引用
当函数返回引用类型时，没有复制返回值，而是返回对象的本身。
```c++
const string &shorterString(const string &s1, const string &s2) {
	return s1.size() < s2.size() ? s1 : s2;
}
```

此时函数返回的是两个 `string` 类型形参中较短的那个字符串的**引用**

>**注意**：千万不能返回局部对象的引用！！！

### 引用返回左值
返回引用的函数返回一个左值
```c++
char &get_val(string &str, string::size_type ix) {
	return str[is];
}

int main() {
	string s("a value");
	cout << s << endl;
	get_val(s, 0) = 'A';
	
	cout << s << endl;
	return 0;
}
```

如果不希望医用返回值被修改，返回值应该声明为 `const`:
```c++
const char &get_val(....
```

>**注意**：同样，千万不要返回指向局部对象的指针

### 静态局部对象
 一个变量如果位于函数的作用域内，但生命周期却阔约了这个函数的多次调用，则应该即将这样的对象定义为 `static`
 
 `static` **局部对象**确保不迟于在程序执行流程第一次经过该对象的定义语句进行初始化。这种对象一旦被创建，在程序结束之前都不会被撤销掉
 
 ### 内联函数
 ```c++
const string &shorterString(const string &s1, const string &s2) {
	return s1.size() < s2.size() ? s1 : s2;
}
```

为这样的小操作定义一个这样的函数的好处是：
- 更容易阅读和理解函数的调用
- 更方便修改
- 使用函数可以确保统一的行为，每个测试都保证以相同的方式实现
- 函数可以重用，不必为其它应用重写代码

但是将 *shortString* 写成函数也有缺点：**调用函数比求解等价表达式要慢得多**。在大多数机器上，调用函数都要做很多的工作：
1. 调用前要先保存寄存器，并在返回的时候恢复；
2. 复制实参；
3. 程序还必须转向一个新位置执行

#### 1. 内联函数避免函数调用开销
将函数指定为内联函数，则它将在程序中每个调用点上“内联”的展开。假设将 *shortString* 定义为内联函数，则调用：
```c++
cout << shortString(s1, s2) << endl;
```

在编译时将展开为：
```c++
cout << s1.size() < s2.size() ? s1 : s2 << endl;
```

关键字为 `inline`
一般来说，内联机制适用于优化小的、只有几行而且经常被调用的函数。大多数编译器都不支持递归函数的内联

#### 2. 把内敛函数放入头文件
>内联函数应该在头文件中定义，这一点不同于其他函数

把内联函数的定义放在头文件中，可以确保在调用函数时所使用的定义是相同的，并且保证在调用点该函数的定义对编译器可见