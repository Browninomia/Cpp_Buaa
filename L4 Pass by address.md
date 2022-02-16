# Pass by address

+ 地址的两种形态：指针与引用

  “Pointer is ugly, but clear; while reference is ambiguous.”

```c
void fun1(int a)
{
  a++;
}
void fun2(int* a)
{
  (*a)++;
}

int main()
{
  int m = 10;
  fun1(m);
  cout << m << endl; //10
  fun2(&m); //传地址
  cout << m << endl; //11
}
```

```cpp
void fun(int& a)
{
  a++; //
}
int main()
{
  int m = 10;
  fun(m);
  cout << m << endl; //11
}
```

+ 传值 V.S. 传地址

|                | Pass by value            | Pass by address |
| -------------- | ------------------------ | --------------- |
| 效果           | read                     | read/write      |
| 性能           | Sizeof(object)           | Sizeof(int)     |
| 函数传值的麻烦 | Copy-constructor拷贝构造 | Nothing         |

​		**never pass by value (built-in type除外)**

+ 拷贝构造

  默认是浅拷贝 bitwise copy，写出来的是深拷贝 logical copy

  浅拷贝：可能导致多个指针指向同一地址，需要避免，因为不知道谁先改了object

  ```cpp
  class Test {
    int i; //value
    int *j; //address (handel 句柄)
  public:
    Test(int i, int j);
    ~Test();
  };
  Test::Test(int ai, int aj) {
    cout << "construct" << endl;
    i=ai;
    j=(int*)malloc(sizeof(int));
    *j=aj;
  }
  Test::~Test(){
    cout << "discontruct" << endl;
    if (j!=NULL){
      free(j);
      j = NULL;
    }
  }
  int main()
  {
    Test t1(1,2);
    Test t2(t1); //C++默认支持拷贝构造(浅拷贝)，Java里的clone，Python里的copy
  }
  ```

  上述代码只会调用一次constructor，两个指针指向同一段内存

  写一个拷贝构造函数，如下

  ```cpp
  class Test {
    int i; //value
    int *j; //address (handel 句柄)
  public:
    Test(int i, int* j);
    Test(Test& t); //拷贝构造
  }
  Test::Test(Test& t)
  {
    this->i = t.i;
    this->j = (int*)malloc(sizeof(int)); //!!!重新分配，一定不能写this->j = t.j
  }
  ```

  函数传值，会直接拷贝构造，需要避免，如下

  ```cpp
  void fun(Test t){
    
  }
  int main() {
    Test t1;
    fun(t1);
  }
  ```

  上述代码调用两次constructor

  **以后函数传oject，只用pass by address**

+ 声明一个私有拷贝构造函数，不实现

  强迫不使用pass by value，一旦函数传值，编译报错

  ```cpp
  private:
  	Test(Test& t);
  ```

+ 一些需要注意的事情

  1. Fly pointer 野指针 (声明后未初始化)

     指针在声明时赋值NULL，从而避免野指针

     一个函数拿到指针必判空NULL 或者断言assert

  2. Memory leak 内存泄漏

  3. Return adress of Local var 返回局部变量地址

  4. multi-pointers for one object 多个指针指向同一个地址 (例如浅拷贝)

     一定！一定！一定！要谨慎使用memcpy: memory copy

     多个指针指向同一个地址时，你不知道谁先修改了object

