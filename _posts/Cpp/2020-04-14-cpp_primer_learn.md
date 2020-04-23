---
layout: post
title: C++ Primer 学习
categories: Basis
description: C++ Primer知识点总结
keywords: C++
---

## Part III Tools for Class Authors

### chapter 13 Copy Control

- copy constructor
- move constructor
- copy-assignment operator
- move-assignment operator
- destructor

#### Section 13.1 Copy Assign and Destroy

##### 13.1.1 拷贝构造函数

```cpp
class Foo{
    public:
    Foo(); //default constructor
    Foo(const Foo&);//copy constructor
    //
}
```

- 例子

```cpp
    string dots(10,'.'); //直接初始化
    string s(dots); //直接初始化
    string s2=dots; //拷贝初始化 有=符号
```

- 拷贝构造函数情形
  - 实参传递费引用类型的形参
  - 返回型为非引用类型函数
  - 花括号列表初始化数组中的元素

##### 13.1.2 拷贝赋值运算符

```c++
  Foo & operator=(const Foo&);//赋值运算符

  Sales_data & Sales_data::operator=(const Sales_data &rhs){
      bookNo=rhs.bookNo; // 调用string::operator=
      revenue=rhs.revenue;
      return *this; //返回对象的引用

  }

```

##### 13.1.3 析构函数

析构函数和构造函数相反操作：析构函数释放对象资源，销毁对象非 static 数据成员

```c++
class Foo{
    public:
       ~Foo();//析构函数  无返回值
};
```

- 调用析构函数情形
  - 变量离开其作用域
  - 对象被销毁，成员被销毁
  - 容器被销毁，元素被销毁
  - delete 运算符，动态分配的对象
  - 临时对象，完整表达式被销毁

#### Section 13.2 Copy Control and Resource Management Section

- 行为像值 normal class
  - 不共享数据
- 行为像指针 eg. shared_ptr
  - 共享底层数据

##### 13.2.1 行为像值的类

##### 13.2.1 行为像指针的类

#### 13.3 Swap

#### Section 13.4 A Copy-Control Example

#### Section 13.5 Classes That Manage Dynamic Memory Section

#### 13.6 Moving Objects

#### Chapter Summary

