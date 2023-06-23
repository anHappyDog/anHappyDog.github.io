## C++ Primer 习题





### 第 7 章

复习题：

1. 使用函数需要： 声明一个函数，实现它的定义，在相应的位置使用函数。

2. 

```c++
void igor();
float tofu(int);
double mpg(double,double);
long summation(long arrname, long arrsize);
double doctor(const string&);
void ofcourse(boss);
std::string plot(map*); 
```

3. 

```c++
void function1(int* arr,int size,int t) {
    for (int i = 0; i != size; ++i) {
        arr[i] = t;
    }
}
```

4.

```c++
void function1(int* begin,int* end,int t) {
    for (int* ptr = begin; ptr <= end; ++ptr) {
        *ptr = t;
    }
}
```

5.

```c++
double function1(double* arr,int size) {
    if (size == 0) {
        panic();
    }
    double mint = arr[0];
	for (int i = 1; i != size; ++i) {
        mint = mint > arr[i] ? arr[i] : mint;
    }
    return mint;
}
```

6.

将基本类型作为函数参数是按值传递，并不会修改原本的值。

7.

字符串可以存储在char数组中，也可以用带双引号的字符串来表示，也可以用指针来表示。

8.

