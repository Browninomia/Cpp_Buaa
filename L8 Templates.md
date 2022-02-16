# 模版类

+ 重用

  函数

  继承：重用父类

  多态：重用向上类型转化的函数

  模版类

+ 模版类 template class collection

  ```cpp
  template <class T> //T表示type
  class Stack
  {
    T pool[100];
    int top;
  public:
    // inline function 内联函数：尽量不要写大函数
    Stack():top(0) {
    } // 构造函数，top=0
    void push(T v) {pool[top++] = v;}
    T pop() {return pool[--top];}
  };
  
  int main()
  {
    Stack<int> s; // 传递T参数
    return 0;
  }
  ```

  Non-inline function

  ```cpp
  template<class T>
  class Array {
    enum { size = 100 };
    T A[size];
  public:
    T& operator[](int index);
  };
  
  template<class T>
  T& Array<T>::operator[](int index) {
    require(index >= 0 && index < size,
      "Index out of range");
    return A[index];
  }
  
  ```

  

+ STL: standard template library

  STL里的类是动态增长的万能容器

  因为Java里everything is reference，因此Java的容器里可以存各种不同的类对象，不论容器定义的类

  C++则只能按照容器定义的类加到容器里，因为C++是pass by value，如果给容器定义[<void*>]类，则也可以像Java一样存各种不同类对象的指针

  + vector

    ```cpp
    #include <iostream>
    #include <vector> // array
    using namespace std;
    
    int main()
    {
      vector<int> v;
      for (int i=0; i<10000; i++) // 动态数组
        v.push_back(i);
      cout << v[9988] << endl; //运算符重载
      return 0;
    }
    ```

  + list

    对vector进行了性能优化，一般只使用vector

  + 传值（传拷贝构造）

    ```cpp
    #include <iostream>
    #include <vector> // array
    #include <list> // linked-list
    using namespace std;
    
    class Test
    {
      static int cnt;
    public:
      Test(){}
      Test(const Test &t){
        cnt++;
        cout << cnt << endl;
      }
    };
    
    int Test::cnt = 0;
    
    int main()
    {
      list<Test> v;
      // vector<Test> v;
      for (int i=0; i<10000; i++)
      {
        Test t;
      v.push_back(t);
      }
  }
    ```

    vector的拷贝构造次数大于list，因为vector会“搬家”
    
    list是链表，每添加一个新元素就申请一个新元素的空间
    
    vector则是一次性申请一个足够大的空间，如果空间不够用了，则会再申请一个更大的空间，然后搬家。

+ 迭代器 iterator

  所有STL类都支持迭代器

  ```cpp
  int main()
  {
    vector<int> v;
    for (int i=0; i<10; i++)
      v.push_back(i);
    vector<int>::iterator it = v.begin(); // 迭代器，指向开始位置的指针
    while (it != v.end())
    {
      cout << *it << endl;
      it++;
    }
    return 0;
  }
  ```

+ namespace 名空间

+ 异常

  + throw
  + assert - NDEBUG

+ friend

  可以访问私有成员