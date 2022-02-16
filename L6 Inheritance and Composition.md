# Reuse

+ Inheritance

  ```cpp
  #include <stdio.h>
  
  class Borrower
  {
    int card_id;
    char *name;
  public:
    void borrow_book();
  };
  
  void Borrower::borrow_book()
  {
    cout << "query DB, How many books?..." << endl;
  }
  
  class Student : public Borrower
  {
  public:
    void borrow_book();
  };
  
  void Student::borrow_book()
  {
    Borrow::borrow_book(); //通常情况下，都会在子类的函数中调用父类的函数，在父类的基础上加东西
    cout << "if>=5, return" << endl;
  }
  
  int main()
  {
    Student s;
    // s is Borrower
    // s is like Borrower
    s.borrow_book(); //reuser under inheritance
    return 0;
  }
  ```

+ Composition

  ```cpp
  class Engine
  {
    int attr;
  public:
    void run();
  };
  
  void Engine::run()
  {
    cout << "engine is running" << endl;
  }
  
  class Car
  {
    Engine e;
    int other;
  public:
    void run();
  };
  
  void Car::run()
  {
    e.run(); //reuse under composition
    cout << "car is running" << endl;
  }
  ```

+ Construct Initialization List 构造器初始化列表

  + 继承

    ```cpp
    #include <iostream>
    using namespace std;
    
    
    class Base
    {
      int i;
    public:
      Base();
      ~Base();
    };
    
    Base::Base()
    {
      cout << "construct base" << endl;
    }
    
    Base::~Base()
    {
      cout << "distruct base" << endl;
    }
    
    
    class Derived : public Base
    {
      int j;
    public:
      Derived();
      ~Derived();
    };
    
    Derived::Derived()
    {
      cout << "construct derived" << endl;
    }
    
    Derived::~Derived()
    {
      cout << "distruct derived" << endl;
    }
    
    int main()
    {
      Derived d; //先自动调用父类构造，然后调用子类构造
      // 子类析构，先调用自己的析构，然后调用父类的析构
      return 0;
    }
    ```

    如果父类有默认构造，则自动调用父类默认构造，否则在子类构造函数中必须写父类构造

    ```cpp
    class Base
    {
      int i;
    public:
      Base(int ai);
    };
    
    Base::Base(int ai)
    {
      cout << "construct base" << endl()
    }
    
    class Derived : public Base
    {
      int j;
    public:
      Derived();
    };
    Derived::Derived(int ai,int aj) : Base(ai)
    {
      cout << "construct derived" << endl;
    }
    ```

  + 组成

    ```cpp
    class Engine
    {
    public:
      Engin(int a);
      void run()
    }
    
    class Car
    {
      Engine e;
    public:
      Car(int a);
      void run()
    }
    
    void Car::Car(): e(1),other(),...//构造初始化列表
    ```

  + 列表构造顺序：按照Test内成员顺序进行构造

    ```cpp
    class Test
    {
    public:
      int i;
      int j;
    public:
      Test(int ai, int aj);
    }
    
    //更加面向对象的写法
    Test::Test(int ai, int aj) :j(aj), i(ai)
    {
    }
    ```

    ```cpp
    Test::Test(int aj) :j(aj), i(j)
    ```

    思考：Test t(3)的i的值是什么？

    i没有被构造，是垃圾

    **无论构造初始器列表的顺序是什么，一定会按照Test内成员顺序进行构造**

+ Protected

  对子类公有，对外部私有

  几乎不写private，都用protected

+ 私有继承（不要用）

  ```cpp
  class Student : public Borrower //公有继承，Borrower所有public函数Student也是public
  
  class Student : Borrower //私有继承，Borrower所有public函数Student是private
  ```

  **永远不要削弱父类接口**

+ 多重继承

  ```cpp
  class Base1()
  {
  public:
    void f();
  };
  void Base1::f()
  {
  }
  
  class Base2()
  {
  public:
    void h();
  };
  void Base2::h()
  {
  }
  
  class Derived : public Base1, public Base2
  {
  };
  
  int main()
  {
    Derived d;
    d.f();
    d.h();
  }
  ```

  菱形继承

  如果Base2也有一个函数f呢？：将继承改为组合

  ```cpp
  class Derived :public Base1
  {
    Base2 b2;
  public:
    void f2();
  }
  ```

  组成是消除多重继承的一个方法