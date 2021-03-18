## Notes
```c++
	int ia[3][4] = {
	{0, 1, 2, 3},
	{4, 5, 6, 7},
	{8, 9, 10, 11}
	};
```

### 使用 typedef 简化多维数组指针
 ```c++
 	typedef int int_array[4];
	int_array *ip = ia;
 ```
 
 使用 `typedef` 类型输出 `ia` 元素
 ```c++
 	for (int_arry *p = ia; p != ia + 3; ++p)
		for (int *q = *p; q != *p + 4; ++q)
			cout << *q <<endl;
 ```