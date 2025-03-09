---
title: 计算机信息管理课程实验 C++篇
tags:
  - 计算机信息管理课程实验
  - C++
categories:
  - C++
abbrlink: 23600
date: 2023-05-13 02:33:30
---
# 一、环境搭建
- 开发工具：vc++6.0，Visual Studio Community 2022版本（推荐）
- vc++6.0：
  - 文件 - 新建 - 工程 - Win32 Console Application - 输入工程名称 - 确定
  - 文件 - 新建 - 文件 - C++ Source File - 输入文件名 - 确定
- VS:
  - 下载c++桌面应用支持包
  - 创建新项目 - 控制台应用 - 下一步 - 输入名称 - 创建
>以下为了同屏看到更多代码，选择省略了函数间的空白行、一行实现某些函数等，参考本文代码的同时，请务必保持良好的代码规范！

# 二、编程练习
1. 定义盒子Box类，要求具有以下成员：长、宽、高分别为x、y、z，可设置盒子形状；可计算盒子体积；可计算盒子的表面积。

```cpp
#include<iostream>
using namespace std;

class Box {
public:
	// 构造函数
	Box(){};
	// 带参数的构造函数
	Box(double a, double b, double c):x(a), y(b), z(c){}
	// 设置盒子大小
	void setBox(double a, double b, double c) {
		x = a; y = b; z = c;
	}
	// 获取面积
	double getArea() {
		return 2 * (x * y + y * z + z * x);
	}
	// 获取体积
	double getVolume() {
		return x * y * z;
	}
	void display() {
		cout << "x: " << x << " y: " << y << " z: " << z;
		cout << " area: " << getArea();
		cout << " volume: " << getVolume() << endl;
	}
private:
	double x, y, z;
};

void main() {
	// 使用默认构造函数不需要Box b2();
	Box b1;
	b1.setBox(1, 2, 3);
	b1.display();

	// 使用带参数的构造函数，需要传入指定参数
	Box b2(2, 3, 4);
	b2.display();

	// 使用new运算符创建动态对象，需要用delete运算符销毁，否则会造成内存泄漏
	Box* b3 = new Box(4, 5, 6);
	b3->display();

	delete b3;
}
```
>输出结果：
>x: 1 y: 2 z: 3 area: 22 volume: 6
x: 2 y: 3 z: 4 area: 52 volume: 24
x: 4 y: 5 z: 6 area: 148 volume: 120

2. 声明一个类模板，分别实现两个整数、浮点数和字符的比较，求出最大数和最小数。
```cpp
#include<iostream>
#include<string>
using namespace std;

template<class numtype>	// 声明类模板
class Compare {
public:
	Compare(numtype a, numtype b):x(a), y(b){}
	// 获取最大值
	numtype max(){ return x > y ? x : y; }
	// 获取最小值
	numtype min(){ return x > y ? y : x; }
	void display() {
		cout << "x: " << x;
		cout << " y: " << y;
		cout << " max: " << max();
		cout << " min: " << min() << endl;
	}
private:
	numtype x, y;
};

void main() {
	Compare<int> c1(1, 2);
	c1.display();	

	Compare<double> c2(22.2, 33.3);
	c2.display();

	Compare<string> c3("hello", "world");
	c3.display();
}
```
>输出结果：
>x: 1 y: 2 max: 2 min: 1
x: 22.2 y: 33.3 max: 33.3 min: 22.2
x: hello y: world max: world min: hello

3. 建立一个对象数组，内放5个学生的数据（学号、成绩），用**指针**指向数组首元素，输出第1，3，5个学生的数据。初值自拟。

```cpp
#include<iostream>
using namespace std;

class Student {
public:
	Student(int n, double s): num(n), score(s){};
	void display() {
		cout << "num: " << num;
		cout << " score: " << score << endl;
	}
private:
	int num;
	double score;
};

void main() {
	Student s[5] = {
		Student(101, 100.0),
		Student(102, 33.50),
		Student(103, 60.0),
		Student(104, 89.50),
		Student(105, 95.50)
	};
	Student* p = s;
	for(int i = 0; i < 5; i ++) {
		if((i % 2) == 0)
			(p+i)->display();
	}
}
```
>输出结果：
num: 101 score: 100
num: 103 score: 60
num: 105 score: 95.5

4. 建立一个对象数组，内放5个学生的数据（学号、成绩），设立一个函数max，用指向对象的**指针**作函数参数，在max函数中找出5个学生中成绩最高者，并输出其学号。初值自拟。

```cpp
#include<iostream>
using namespace std;

class Student {
public:
	Student(int n, double s): num(n), score(s){};
	double getScore() {
		return score;
	}
	void display() {
		cout << "num: " << num;
		cout << " score: " << score << endl;
	}
private:
	int num;
	double score;
};

void max(Student* p) {
	int k = 0;
	double max_score = 0;
	for(int i = 0; i < 5; i ++) {
		int cur_score = (p+i)->getScore();
		if (max_score < cur_score) {
			k = i;
			max_score = cur_score;
		}
	}
	cout << "the student with the highest score is ";
	(p+k)->display();
}

void main() {
	Student s[5] = {
		Student(101, 55.0),
		Student(102, 33.50),
		Student(103, 60.0),
		Student(104, 89.50),
		Student(105, 95.50)
	};
	Student* p = s;
	max(p);
}
```
>输出结果：
>the student with the highest score is num: 105 score: 95.5

5. 用new建立一个动态一维数组，并初始化int[10]={1,2,3,4,5,6,7,8,9,10}，用**指针**输出，最后**销毁**数组所占空间。

```cpp
#include<iostream>
using namespace std;

void main() {
	int* p;
	// C++11可改为p = new int[10]{1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
	p = new int[10];
	for(int i = 0; i < 10; i ++) {
		*(p+i) = i+1;
	}
	for(int j = 0; j < 10; j ++) {
		cout << *(p+j) << " ";
	}
	cout << endl;
	delete[] p;
}
```
>运行结果：
>1 2 3 4 5 6 7 8 9 10

6. 定义一个复数类Complex，重载运算符“+”，使之能用于复数的加法运算。将运算符函数重载为**非成员、非友元**的普通函数。编写程序，求两个复数之和。初值自拟。

> 复数是由实部和虚部构成的数学对象。复数可以表示为 a + bi 的形式，其中 a 和 b 都是实数，i 是虚数单位，满足 i² = -1。
在这个表示中，a 是复数的实部，b 是复数的虚部。实部和虚部都是实数。两个复数相等当且仅当它们的实部相等并且它们的虚部相等。
与实数不同的是，复数之间存在着更加复杂的乘法和除法运算。例如，如果 z₁ = 2 +i 3，z₂ = - 4 + i 5，则彼此相乘将得到：z₁z₂ = - 22 + i 7。因此，除了一般的加法和减法外，还需要引入其他算术规则和操作来处理复数。
复数具有很多实际应用，例如物理、工程、金融、电子学等领域。在计算机科学中，复数被广泛应用于信号处理、数字图像处理、动画和游戏编程等方面。

代码把`+-*/`都写了，供参考
```cpp
#include<iostream>
using namespace std;

class Complex {
public:
	Complex(double r, double i): real(r), imag(i) {}
	double getReal() { return real; }
	double getImag() { return imag; }
	void display() {
		cout << "(" << real << " ," << imag << ")" << endl;
	}
private:
	double real, imag;
};

// (a, b)-(c, d) = (a+c, b+d)
Complex operator+(Complex& c1, Complex& c2) {
	return Complex(c1.getReal() + c2.getReal(), c1.getImag() + c2.getImag());
}

// (a, b)-(c, d) = (a-c, b-d)
Complex operator-(Complex& c1, Complex& c2) {
	return Complex(c1.getReal() - c2.getReal(), c1.getImag() - c2.getImag());
}

// (a, b)*(c, d) = (ac - bd, ad + bc)
Complex operator*(Complex& c1, Complex& c2) {
	return Complex(c1.getReal() * c2.getReal() - c1.getImag() * c2.getImag(),
		c1.getReal() * c2.getImag() + c1.getImag() * c2.getReal());
}

// (a, b)/(c, d) = ((ac + bd) / (c^2 + d^2), (bc - ad) / (c^2 + d^2))
Complex operator/(Complex& c1, Complex& c2) {
	double m = c2.getReal() * c2.getReal() + c2.getImag() * c2.getImag();
	double real = (c1.getReal() * c2.getReal() + c1.getImag() * c2.getImag()) / m;
	double imag = (c1.getImag() * c2.getReal() - c1.getReal() * c2.getImag()) / m;
	return Complex(real, imag);
}

void main() {
	Complex c1(2, 3);
	cout << "c1 = ";
	c1.display();
	Complex c2(-4, 5);
	cout << "c2 = ";
	c2.display();

	cout << "c1 + c2 = ";
	(c1 + c2).display();

	cout << "c1 - c2 = ";
	(c1 - c2).display();

	cout << "c1 * c2 = ";
	(c1 * c2).display();

	cout << "c1 / c2 = ";
	(c1 / c2).display();
}
```
>运行结果：
c1 = (2 ,3)
c2 = (-4 ,5)
c1 + c2 = (-2 ,8)
c1 - c2 = (6 ,-2)
c1 * c2 = (-23 ,-2)
c1 / c2 = (0.170732 ,-0.536585)

7. 定义一个复数类Complex，重载运算符“+”，“-”，使之能用于复数的加，减运算，运算符重载函数作为Complex类的**成员函数**。编程序，分别求出两个复数之和，差。初值自拟。

```cpp
#include<iostream>
using namespace std;

class Complex {
public:
	Complex(double r, double i): real(r), imag(i) {}
	Complex operator+(Complex& c);
	Complex operator-(Complex& c);
	void display() {
		cout << "(" << real << " ," << imag << ")" << endl;
	}
private:
	double real, imag;
};

Complex Complex::operator+(Complex& c) {
	return Complex(real + c.real, imag + c.imag);
}

Complex Complex::operator-(Complex& c) {
	return Complex(real - c.real, imag - c.imag);
}

void main() {
	Complex c1(2, 3);
	cout << "c1 = ";
	c1.display();
	Complex c2(-4, 5);
	cout << "c2 = ";
	c2.display();

	cout << "c1 + c2 = ";
	(c1 + c2).display();

	cout << "c1 - c2 = ";
	(c1 - c2).display();
}
```
>输出结果：
>c1 = (2 ,3)
c2 = (-4 ,5)
c1 + c2 = (-2 ,8)
c1 - c2 = (6 ,-2)

8. 定义一个复数类Complex，重载运算符“＋”，使之能用于复数的加法运算。参加运算的两个运算量可以都是类对象，也可以其中有一个是整数，顺序任意。例如：c1+c2，i+c1，c1+i均合法（设i为整数，c1，c2为复数）。编程序，分别求两个复数之和、整数和复数之和。初值自拟。
>友元函数的概念是在 C++ 标准化之后引入的，VC6.0 是一个较为古老的编译器版本，发布于 1998 年，仅支持部分 C++98 的特性，并不支持 C++11、C++14或更高标准提出的许多新特性，包括现代意义上的友元函数。
虽然 VC6.0 可以定义和使用传统意义上的自由函数（非成员函数），但实际上它并没有真正支持友元函数。因此，在使用 VC6.0 时，您需要采用传统的方法来定义和访问类的私有成员。
```cpp
#include<iostream>
using namespace std;

class Complex {
public:
	Complex() { real = 0; imag = 0; }
	Complex(double r, double i) : real(r), imag(i) {}
	double getReal() { return real; }
	double getImag() { return imag; }
	Complex operator+(Complex& c);
	friend Complex operator+(int& i, Complex& c);
	friend Complex operator+(Complex& c, int& i);
	void display() {
		cout << "(" << real << " ," << imag << ")" << endl;
	}
private:
	double real, imag;
};

Complex Complex::operator+(Complex& c) {
	return Complex(real + c.real, imag + c.imag);
}

Complex operator+(Complex& c, int& i) {
	return Complex(c.getReal() + i, c.getImag());
}

Complex operator+(int& i, Complex& c) {
	return Complex(c.getReal() + i, c.getImag());
}

void main() {
	Complex c1(2, 3);
	cout << "c1 = ";
	c1.display();
	Complex c2(-4, 5);
	cout << "c2 = ";
	c2.display();

	cout << "c1 + c2 = ";
	(c1 + c2).display();

	int i = 2;
	cout << "c1 + 2 = ";
	(c1 + i).display();

	cout << "2 + c1 = ";
	(i + c1).display();
}
```
>输出结果：
>c1 = (2 ,3)
c2 = (-4 ,5)
c1 + c2 = (-2 ,8)
c1 + 2 = (4 ,3)
2 + c1 = (4 ,3)

9. 有两个矩阵a和b，均为2行3列。求两个矩阵之和。重载运算符“+”，使之能用于矩阵相加。如c=a+b。初值自拟。
```cpp
#include<iostream>
using namespace std;

class Matrix {
private:
	int data[2][3];
public:
	Matrix();
	Matrix(const int (&arr)[2][3]);
	friend Matrix operator+(const Matrix& m1, const Matrix& m2);
	void display();
};

Matrix::Matrix() {
	for (int i = 0; i < 2; i++) {
		for (int j = 0; j < 3; j++) {
			data[i][j] = 0;
		}
	}
}
Matrix::Matrix(const int (&arr)[2][3]) {
	for (int i = 0; i < 2; i++) {
		for (int j = 0; j < 3; j++) {
			data[i][j] = arr[i][j];
		}
	}
}

Matrix operator+(const Matrix& m1, const Matrix& m2) {
	Matrix m;
	for (int i = 0; i < 2; i++) {
		for (int j = 0; j < 3; j++) {
			m.data[i][j] = m1.data[i][j] + m2.data[i][j];
		}
	}
	return m;
}

void Matrix::display() {
	cout << "{ ";
	for (int i = 0; i < 2; i++) {
		cout << (i == 0 ? "" : ", ") << "{";
		for (int j = 0; j < 3; j++) {
			cout << (j == 0 ? "" : ", ") << data[i][j];
		}
		cout << "}";
	}
	cout << " }" << endl;
}

void main() {
	int arr1[2][3] = { {1, 2, 3}, {4, 5, 6} };
	Matrix m1(arr1);
	int arr2[2][3] = { {7, 8, 9}, {10, 11, 12} };
	Matrix m2(arr2);

	Matrix m3 = m1 + m2;
	cout << "m3 = ";
	m3.display();
}
```
>输出结果：
>m3 = { {8, 10, 12}, {14, 16, 18} }

10. 将运算符“＋”重载为适用于复数加法，重载函数不作为成员函数，而放在类外，作为Complex类的友元函数。初值自拟。
```cpp
#include<iostream>
using namespace std;

class Complex {
private:
	double real, imag;
public:
	Complex();
	Complex(const double& r, const double& imag);
	friend Complex operator+(const Complex& c1, const Complex& c2);
	void display();
};

Complex::Complex() {
	real = 0; imag = 0;
}

Complex::Complex(const double& r, const double& i) : real(r), imag(i) {}

void Complex::display() {
	cout << "(" << real << ", " << imag << ")" << endl;
}

Complex operator+(const Complex& c1, const Complex& c2) {
	return Complex(c1.real + c2.real, c1.imag + c2.imag);
}


void main() {
	Complex c1(3, 4);
	Complex c2(5, 6);
	cout << "c1 + c2 = ";
	(c1 + c2).display();
}

```
>输出结果：
>c1 + c2 = (8, 10)

11. 定义一个字符串类String，用来存放不定长的字符串，重载运算符“＝＝”，用于两个字符串的等于比较运算。初值自拟。
```cpp
#include<iostream>
#include<string>
using namespace std;

class String {
private:
	char* p;
public:
	String() { p = nullptr; }
	String(const char* s) { 
		p = new char[strlen(s) + 1];
		strcpy_s(p, strlen(s) + 1, s);
	}
	String(const String& other) {
		if (other.p == nullptr) {
			p = nullptr;
		}
		else {
			p = new char[strlen(other.p) + 1];
			strcpy_s(p, strlen(other.p) + 1, other.p);
		}
	}
	void display() const {
		cout << p << endl;
	}
	friend bool operator==(const String& s1, const String& s2);
};

bool operator==(const String& s1, const String& s2) {
	return strcmp(s1.p, s2.p) == 0 ? true : false;
}

void main() {
	String s1("Hello");
	String s2("World");
	String s3(s1);
	String s4;
	s4 = "World";

	if (s1 == s2)
		cout << "s1 equals to s2." << endl;
	else
		cout << "s1 doesn't equal to s2." << endl;

	if (s1 == s3)
		cout << "s1 equals to s3." << endl;
	else
		cout << "s1 doesn't equal to s3." << endl;

	if (s2 == s4)
		cout << "s2 equals to s4." << endl;
	else
		cout << "s2 doesn't equal to s4." << endl;
}
```
>输出结果：
>s1 doesn't equal to s2.
s1 equals to s3.
s2 equals to s4.
12. 定义一个字符串类String，用来存放不定长的字符串，重载运算符"<"，用于两个字符串的小于的比较运算。初值自拟。
```cpp
#include<iostream>
#include<string>
using namespace std;

class String {
private:
	char* p;
public:
	String() { p = nullptr; }
	String(const char* s) { 
		p = new char[strlen(s) + 1];
		strcpy_s(p, strlen(s) + 1, s);
	}
	String(const String& other) {
		if (other.p == nullptr) {
			p = nullptr;
		}
		else {
			p = new char[strlen(other.p) + 1];
			strcpy_s(p, strlen(other.p) + 1, other.p);
		}
	}
	void display() const {
		cout << p << endl;
	}
	friend bool operator==(const String& s1, const String& s2);
	friend bool operator<(const String& s1, const String& s2);
	friend bool operator>(const String& s1, const String& s2);
};

bool operator==(const String& s1, const String& s2) {
	return strcmp(s1.p, s2.p) == 0;
}
bool operator<(const String& s1, const String& s2) {
	return strcmp(s1.p, s2.p) < 0;
}
bool operator>(const String& s1, const String& s2) {
	return strcmp(s1.p, s2.p) > 0;
}

void main() {
	String s1("Hello");
	String s2("World");
	String s3(s1);
	String s4;
	s4 = "World";

	if(s1 == s2)
		cout << "s1 == s2." << endl;
	else if (s1 < s2)
		cout << "s1 < s2." << endl;
	else if (s1 > s2)
		cout << "s1 > s2." << endl;

	if (s1 == s3)
		cout << "s1 == s3." << endl;
	else if (s1 < s3)
		cout << "s1 < s3." << endl;
	else if (s1 > s3)
		cout << "s1 > s3." << endl;

	if (s2 == s4)
		cout << "s1 == s4." << endl;
	else if (s2 < s4)
		cout << "s2 < s4." << endl;
	else if (s2 > s4)
		cout << "s2 > s4." << endl;
}
```

>输出结果：
>s1 < s2.
s1 == s3.
s1 == s4.

13. 定义一个字符串类String，用来存放不定长的字符串，重载运算符">"，用于两个字符串的大于的比较运算。初值自拟。（同12题，'<'，'>'都包含）

```cpp
#include<iostream>
#include<string>
using namespace std;

const double PI = 3.1415926;

class Point {
protected:
	double x, y;
public:
	Point(double x = 0, double y = 0) {
		this->x = x;
		this->y = y;
	}
	friend ostream& operator<<(ostream& os, const Point& p);
	friend istream& operator>>(istream& is, Point& p);
};
ostream& operator<<(ostream& os, const Point& p) {
	os << "(" << p.x << ", " << p.y << ")";
	return os;
}
istream& operator>>(istream& is, Point& p) {
	is >> p.x >> p.y;
	return is;
}

class Circle : public Point {
protected:
	double r;
public:
	Circle(double x = 0, double y = 0, double r = 0) :Point(x, y) {
		this->r = r;
	}
	double area() const {
		return PI * r * r;
	}
	friend ostream& operator<<(ostream& os, const Circle& c);
	friend istream& operator>>(istream& is, Circle& c);
};
ostream& operator<<(ostream& os, const Circle& c) {
	os << "Center:" << (Point)c << ", Radius:" << c.r << ", Area:" << c.area();
	return os;
}
istream& operator>>(istream& is, Circle& c) {
	is >> (Point&)c >> c.r;
	return is;
}

class Cylinder : public Circle {
protected:
	double h;
public:
	Cylinder(double x = 0, double y = 0, double r = 0, double h = 0) : Circle(x, y, r) {
		this->h = h;
	}
	double volume() const {
		return area() * h;
	}
	friend ostream& operator<<(ostream& os, const Cylinder& c);
	friend istream& operator>>(istream& is, Cylinder& c);
};
ostream& operator<<(ostream& os, const Cylinder& c) {
	os << "Center:" << (Point)c << " Radius:" << c.r << " Height:" << c.h << " Volume:" << c.volume();
	return os;
}
istream& operator>>(istream& is, Cylinder& c) {
	is >> (Circle&)c >> c.h;
	return is;
}

int main() {
	Point p(1, 2);
	cout << p << endl;
	Circle c(1, 2, 3);
	cout << c << endl;
	Cylinder cy(1, 2, 3, 4);
	cout << cy << endl;
	return 0;
}
```

>输出结果：
>(1, 2)
Center:(1, 2), Radius:3, Area:28.2743
Center:(1, 2) Radius:3 Height:4 Volume:113.097

14. 写一个程序，定义抽象类型Shape，由他派生三个类：Circle（圆形），Rectangle（矩形），Trapezoid（梯形），用一个函数printArea分别输出三者的面积，3个图形的数据在定义对象是给定。

```cpp
#include<iostream>
#include<string>
using namespace std;

const double PI = 3.1415926;

class Shape {
public:
	virtual double area() const = 0; // 纯虚函数
};

class Circle: public Shape {
protected:
	double r;
public:
	Circle(double r = 0) {
		this->r = r;
	}
	double area() const {
		return PI * r * r;
	}
};

class Rectangle: public Shape {
protected:
	double w, h;
public:
	Rectangle(double w = 0, double h = 0) {
		this->w = w;
		this->h = h;
	}
	double area() const {
		return w * h;
	}
};

class Trapezoid: public Shape {
protected:
	double t, b, h;
public:
	Trapezoid(double t = 0, double b = 0, double h = 0) {
		this->t = t;
		this->b = b;
		this->h = h;
	}
	double area() const {
		return (t + b) * h / 2;
	}
};

void printArea(Shape& s) {
	cout << "The area is: " << s.area() << endl;
}

int main() {
	Circle c(5.0);
	printArea(c);
	Rectangle r(3.0, 4.0);
	printArea(r);
	Trapezoid t(2, 4, 5);
	printArea(t);
	return 0;
}
```
>输出结果：
>The area is: 78.5398
The area is: 12
The area is: 15

15. 定义一个人员类CPerson，包括数据成员：姓名、编号、性别和用于输入输出的成员函数。在此基础上派生出学生类CStudent(增加成绩)和老师类CTeacher(增加教龄)，并实现对学生和教师信息的输入输出。
```cpp
#include<iostream>
#include<string>
using namespace std;

class CPerson {
protected:
	string name;
	int id;
	char gender;
public:
	void input() {
		cout << "Enter Name: ";
		cin >> name;
		cout << "Enter ID: ";
		cin >> id;
		cout << "Enter Gender (M or F): ";
		cin >> gender;
	}
	void output() {
		cout << "Name: " << name << endl;
		cout << "ID: " << id << endl;
		cout << "Gender: " << gender << endl;
	}
};

class CStudent: public CPerson {
protected:
	double score;
public:
	void input() {
		CPerson::input();
		cout << "Enter Score: ";
		cin >> score;
	}
	void output() {
		CPerson::output();
		cout << "Score: " << score << endl;
	}
};

class CTeacher : public CPerson {
protected:
	int seniority;
public:
	void input() {
		CPerson::input();
		cout << "Enter Seniority: ";
		cin >> seniority;
	}
	void output() {
		CPerson::output();
		cout << "Seniority; " << seniority << endl;
	}
};

int main() {
	CStudent cs;
	cout << "--- input student's infomation ---" << endl;
	cs.input();
	cout << "--- output student's infomation ---" << endl;
	cs.output();

	CTeacher ct;
	cout << "--- input teacher's infomation ---" << endl;
	ct.input();
	cout << "--- output teacher's infomation ---" << endl;
	ct.output();

	return 0;
}

```
> 输出结果：
--- input student's infomation ---
Enter Name: Kk
Enter ID: 101
Enter Gender (M or F): M
Enter Score: 100
--- output student's infomation ---
Name: Kk
ID: 101
Gender: M
Score: 100
--- input teacher's infomation ---
Enter Name: JJ
Enter ID: 202
Enter Gender (M or F): F
Enter Seniority: 20
--- output teacher's infomation ---
Name: JJ
ID: 202
Gender: F
Seniority; 20

16. 某商店经销一种货物，货物成箱购进，成箱卖出，购进和卖出时以重量为单位，各箱的重量不一样，因此，商店需要记下目前库存货物的总量，要求把商店货物购进和卖出的情况模拟出来。
```cpp
#include<iostream>
using namespace std;

class Goods {
private:
	int weight; // 单位：克
public:
	Goods(int w = 0) {
		weight = w;
	}
	int getWeight() const {
		return weight;
	}
};

class Store {
private:
	Goods* inventory; // 库存商品
	int size;	// 库存商品数量
	int capacity; // 当前最大容量
public:
	Store(int c = 50) {
		capacity = c;
		size = 0;
		inventory = new Goods[c];
	}
	~Store() {
		delete[] inventory;
	}
	void addGoods(Goods g) {
		if (size < capacity) {
			inventory[size++] = g;
		}
		else {	// 容量不足，需要扩容
			Goods* temp = new Goods[capacity * 2];
			for (int i = 0; i < capacity; i++) {
				temp[i] = inventory[i];
			}
			delete[] inventory;
			inventory = temp;
			capacity = capacity * 2;
			inventory[size++] = g;
		}
		cout << "Add a goods with weight of " << g.getWeight() << endl;
	}
	void sellGoods(int amount) {	// 出售商品
		if (amount < size) {
			for (int i = 0; i < amount; i++) {
				size--;	// 出售一件商品，库存-1
				cout << "Sell a goods with weight of " << inventory[size].getWeight() << endl;
			}
		}
		else {
			cout << "Inventory is not enough!" << endl;
		}
	}
	int getInventorySize() const {	// 获取当前库存量
		return size;
	}
};

int main() {
	Store store(5);
	store.addGoods(Goods(100));
	store.addGoods(Goods(500));
	store.addGoods(Goods(300));
	cout << "Current inventory: " << store.getInventorySize() << endl;

	store.sellGoods(2);
	cout << "Current inventory: " << store.getInventorySize() << endl;

	store.addGoods(Goods(400));
	cout << "Current inventory: " << store.getInventorySize() << endl;

	store.sellGoods(10);
	return 0;
}

```
> 输出结果：
Add a goods with weight of 100
Add a goods with weight of 500
Add a goods with weight of 300
Current inventory: 3
Sell a goods with weight of 300
Sell a goods with weight of 500
Current inventory: 1
Add a goods with weight of 400
Current inventory: 2
Inventory is not enough!

17. 设计一个Time类，包括三个私有数据成员：hour、minute、sec，用构造函数初始化，内设公用函数display(Date &d)，设计一个Date类，包括三个私有数据成员：month、day、year，也用构适函数初始化；分别定义两个带参数的对象t1(12,30,55)、d1(3,25,2010)，通过友员成员函数的应用，输出d1和t1的值。
>这里vs有个坑，就是在Time类里面直接实现函数display，会出现`“Date::year”: 无法访问 private 成员(在“Date”类中声明)	`，后来改为外部实现才编译通过。
>还有两个地方要注意：
>开头需要加上class Date;
>class Time在class Date上面。
```cpp
#include<iostream>
#include<string>
using namespace std;

class Date;
class Time {
private:
	int hour, minute, sec;
public:
	Time(int h = 0, int m = 0, int s = 0) : hour(h), minute(m), sec(s) {}
	void display(Date& d);
};

class Date {
private:
	int month, day, year;
public:
	Date(int m = 0, int d = 0, int y = 0) : month(m), day(d), year(y) {}

	friend void Time::display(Date& d);
};

void Time::display(Date& d) {
	cout << d.month << "/" << d.day << "/" << d.year << " ";
	cout << hour << ":" << minute << ":" << sec << endl;
}

int main() {
	Date d(5, 20, 2020);
	Time t(10, 10, 20);
	t.display(d);
	return 0;
}

```
> 输出结果：
> 5/20/2020 10:10:20

18. 设计一个Time类，包括三个私有数据成员：hour、minute、sec，用构造函数初始化，设计一个Date类，包括三个私有数据成员：month、day、year，也用构适函数初始化；设计一个普通函数display(…)，将display分别设置为Time类和Date类的友元函数，在主函数中分别定义两个带参数的对象t1(12,30,55)、d1(3,25,2010)，调用desplay，输出年、月、日和时、分、秒。
```cpp
#include<iostream>
#include<string>
using namespace std;

class Date;
class Time {
private:
	int hour, minute, sec;
public:
	Time(int h = 0, int m = 0, int s = 0) : hour(h), minute(m), sec(s) {}
	friend void display(const Date& d, const Time& t);
};

class Date {
private:
	int month, day, year;
public:
	Date(int m = 0, int d = 0, int y = 0) : month(m), day(d), year(y) {}

	friend void display(const Date& d, const Time& t);
};

void display(const Date& d, const Time& t) {
	cout << d.month << "/" << d.day << "/" << d.year << " ";
	cout << t.hour << ":" << t.minute << ":" << t.sec << endl;
}

int main() {
	Date d(5, 20, 2020);
	Time t(10, 10, 20);
	display(d, t);
	return 0;
}

```
> 输出结果：
> 5/20/2020 10:10:20

19. 定义一个类（Point），再定义一个类（Distance）描述两点之间的距离，其数据成员为两个点类对象，两点之间距离的计算可设计由构造函数来实现。
```cpp
#include<iostream>
#include<string>
using namespace std;

class Point {
private:
	double x, y;
public:
	Point(double x = 0, double y = 0) : x(x), y(y) {}
	double getX() { return x; }
	double getY() { return y; }
};

class Distance {
private:
	double dist;
	Point pA, pB;
public:
	Distance(Point& p1, Point& p2): pA(p1), pB(p2) {
		double dx = abs(p1.getX() - p2.getX());
		double dy = abs(p1.getY() - p2.getY());
		dist = sqrt(dx * dx + dy * dy);
	}
	double getDist() {
		return dist;
	}
};

int main() {
	Point p1(0, 0);
	Point p2(3, 4);
	Distance d(p1, p2);
	cout << "The distance between p1 and p2 is " << d.getDist() << endl;
	return 0;
}

```
> 输出结果：
> The distance between p1 and p2 is 5

20. 定义点类（Point），再定义一个函数（Distance）描述两点之间的距离，其数据成员为两个点类对象，将两点之间距离函数声明为Point类的友元函数。
```cpp
#include<iostream>
#include<string>
using namespace std;

class Point {
private:
	double x, y;
public:
	Point(double x = 0, double y = 0) : x(x), y(y) {}
	double getX() { return x; }
	double getY() { return y; }
	friend double Distance(Point& p1, Point& p2);
};

double Distance(Point& p1, Point& p2) {
	double dx = abs(p1.x - p2.x);
	double dy = abs(p1.y - p2.y);
	return sqrt(dx * dx + dy * dy);
}

int main() {
	Point p1(0, 0);
	Point p2(3, 4);
	cout << "The distance between p1 and p2 is " << Distance(p1, p2) << endl;
	return 0;
}

```
> 输出结果：
> The distance between p1 and p2 is 5

21. 实现重载函数Double(x)，返回值为输人参数的两倍；参数分别为整型、浮点型、双精度型，返回值类型与参数一样。（用类模板实现）
```cpp
#include<iostream>
#include<string>
using namespace std;

template<class numtype>
class Double {
private:
	numtype x;
public:
	Double(numtype x):x(x){}
	numtype doubleValue() {
		return 2 * x;
	}
};

int main() {
	Double<int> d1(10);
	cout << "The double of 10 is " << d1.doubleValue() << endl;

	Double<float> d2(10.55);
	cout << "The double of 10.55 is " << d2.doubleValue() << endl;

	Double<double> d3(20.3333);
	cout << "The double of 20.3333 is " << d3.doubleValue() << endl;

	return 0;
}
```
> 输出结果：
> The double of 10 is 20
The double of 10.55 is 21.1
The double of 20.333333 is 40.6666

22. 有一个Time类，包含数据成员minute（分）和sec（秒），模拟秒表，每次走一秒，满60秒进一分钟，此时秒又从0开始算。要求输出分和秒的值。初值自拟。
>这里为了实现每秒输出一次，引用了头文件window.h，Sleep(1000)表示休眠1000ms
>引用头文件iomanip，setw(2)设置宽度为2，setfill('0')设置填充'0'
>实际练习可以忽略
```cpp
#include<iostream>
#include<windows.h>
#include<iomanip>
using namespace std;

class Time {
private:
	int minute, sec;
public:
	Time(int m = 0, int s = 0) : minute(m), sec(s) {}
	void run() {
		while (true) {
			cout << "Current time:" << setw(2) << setfill('0') << minute;
			cout << ":" << setw(2) << setfill('0') << sec << endl;
			sec++;
			if (sec >= 60) {
				sec = 0;
				minute++;
			}
			if(minute >= 60) {
				minute = 0;
			}
			Sleep(1000);
		}
	}
};

int main() {
	Time t(59, 55);
	t.run();
	return 0;
}
```
> 输出结果：
> Current time:59:55
Current time:59:56
Current time:59:57
Current time:59:58
Current time:59:59
Current time:00:00
Current time:00:01
Current time:00:02
Current time:00:03
Current time:00:04
Current time:00:05
...

23. 分别用成员函数和友元函数重载运算符，使对实型的运算符“-”适用于复数运算。
```cpp
#include <iostream>
using namespace std;

class Complex {
private:
    double real;  // 实部
    double imag;  // 虚部
public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) {}
    // 成员函数方式重载运算符“-”
    Complex operator-(const Complex& other) const {
        return Complex(real - other.real, imag - other.imag);
    }
    // 友元函数方式重载运算符“-”
    friend Complex operator-(double d, const Complex& c) {
        return Complex(d - c.real, -c.imag);
    }
    // 友元函数方式重载输出运算符“<<”
    friend ostream& operator<<(ostream& os, const Complex& c) {
        os << "(" << c.real << ", " << c.imag << ")";
        return os;
    }
};

int main() {
    Complex a(3, 4);
    Complex b(1, 2);

    // 使用成员函数方式重载的减法运算符
    Complex c1 = a - b;
    cout << "c1 = a - b = " << c1 << endl;

    // 使用友元函数方式重载的减法运算符
    double d = 5.0;
    Complex c2 = d - b;
    cout << "c2 = " << d << " - b = " << c2 << endl;

    return 0;
}
```
> 输出结果：
> c1 = a - b = (2, 2)
c2 = 5 - b = (4, -2)

24. 商店销售某一商品，商店每天公布统一的折扣（discount）。同时允许销售人员在销售时灵活掌握售价（price），在此基础上，对一次购10件以上者，还可以享受9.8折优惠。请编程序，计算出当日此商品的总销售款sum，以及每件商品的平均售价。要求用静态数据成员和静态成员函数。（提示：将折扣discount、总销售款sum和商品销售总件数n声明为静态数据成员，再定义静态成员函数average（求平均售价）和display（输出结果）。现已知当天3名销货员的销售情况为：
销货员号（num）|销货件数（quantity）|销售单价（price）
--|--|--
101|5|23.5
102|12|24.56
103|100|21.5
```cpp
#include <iostream>
using namespace std;

class Product {
private:
    static double discount; // 折扣
    static double sum;      // 总销售款
    static int n;           // 总销售数
    int num;                // 销售员号
    int quantity;           // 销售件数
    double price;           // 销售单价
public:
    Product(int n, int q, double p) :num(n), quantity(q), price(p) {}
    void sale() {
        double total = quantity * price;
        if (quantity > 10) {
            total *= discount;
        }
        sum += total;
        n += quantity;
    }
    static double average() {
        return sum / n;
    }
    static void display() {
        cout << "The total sales amount is " << sum;
        cout << ", and average is " << average() << endl;
    }
};
double Product::discount = 0.98;
double Product::sum = 0;
int Product::n = 0;

int main() {
    Product p1(101, 5, 23.5);
    Product p2(102, 12, 24.56);
    Product p3(104, 100, 21.5);

    p1.sale();
    p2.sale();
    p3.sale();

    Product::display();
    return 0;
}
```
> 输出结果：
> The total sales amount is 2513.33, and average is 21.4814

25. 有一个Time类，包含数据成员minute（分）和sec（秒），模拟秒表，每次走一秒，满60秒进一分钟，此时秒又从0开始算。要求输出分和秒的值，且对后置自增运算符的重载。
>后置自增运算符：Time& operator(int);
>前置自增运算符：Time& operator();
```cpp
#include <iostream>
using namespace std;

class Time {
private:
    int minute;
    int sec;
public:
    Time(int m = 0, int s = 0) : minute(m), sec(s) {}
    void display() {
        cout << "time: " << minute << ":" << sec << endl;
    }
    Time& operator++(int) { // Time++
        sec++;
        if (sec >= 60) {
            minute += sec / 60;
            sec %= 60;
        }
        return *this;
    }
    Time& operator++() {    // ++Time
        sec++;
        if (sec >= 60) {
            minute += sec / 60;
            sec %= 60;
        }
        return *this;
    }
};

int main() {
    Time t(6, 50);
    t.display(); // 输出当前时间

    t++;         // 模拟时间流逝，自增1秒
    t.display(); // 输出当前时间

    ++t;         // 模拟时间流逝，自增1秒
    t.display(); // 输出当前时间

    return 0;
}
```
> 输出结果：
> time: 6:50
time: 6:51
time: 6:52