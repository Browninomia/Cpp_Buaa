# Something Else

+ New & Delete

  ```cpp
  int main()
  {
    //Test *p = (Test*)malloc(sizeof(Test));
    Test *p = new Test(1,2); // new 直接返回Test*类型，同时构造一个Test对象
  }
  ```

  new = malloc(void*) + constructor(this)，返回一个object指针

  delete = destructor + free

  注意：new delete一定要成对使用，否则malloc+delete没有constructor会出错。

  

  指向多个对象的指针的new delete写法：

  ```cpp
  Test::Test()
  {
    j = NULL;
  }
  int main()
  {
    Test *p = new Test[10]; //一定要写默认构造
    delete []p;
  }
  ```

+ const 只读不写

  pass by address可读可写

  用const修饰表示pass by address只读read only（通常拷贝构造传const &）

  + 修饰函数参数

  ```cpp
  void fun(const int *p)
  {
    *p = 10; //错的，不能过编译
  }
  ```

  如果是read only就加const，如果write就不加const

  例如string copy：strcpy(char *s1, const char *s2)，说明是s2拷贝给s1

  + 修饰函数

  如果是const函数，就只能对变量进行read，不能write

  ```cpp
  void fun() const
  {
    //read only
  }
  ```

  + 修饰对象

  ```cpp
  int main()
  {
  const double pi=3.14; //常量对象
    const Test t;
    t.test_fun(); //编译不通过，需要显示声明test_fun()是可以被常量变量调用的
  }
  ```

  常量对象不能调用方法

  + 修饰对象的方法

  显示声明可以被常量变量调用：

  ```cpp
  class Test
  {
  public:
    void test_fun() const;
  }
  void Test::test_fun() const
  {
    cout<<i<<endl;
    i=4;//编译不通过，常量函数不能修改
  }
  ```

  返回built-in type的函数一定是const，不能fun()++

  但是如果返回自定义object就可以调用类方法（但是，不推荐返回object value，尽量pass by add返回地址）

+ extern 外链接

  + 变量

    不用声明外链接变量在哪，会自动寻找。

    一般会将所有全局变量放在一个源文件里，然后用extern

    头文件里不可以定义全局变量，但是可以通过extern声明全局变量

    慎用：文件间通信可以使用全局变量，但是无关文件也可以操纵全局变量，有利有弊。

  + 函数

    函数默认外链接 (可见域：函数默认全局可见，变量默认本文件可见)

  + // extern “C”

    修饰C函数

+ static

  + 局部变量

    ```cpp
    void fun()
    {
      static int i=0; //static修饰局部变量
      i++;
      cout << i << endl;
    }
    ```

  ​		static 一次创建，永不消亡，直至程序结束

  ​		静态局部变量的地址与全局变量存储在同一区域

  ​		创建时间：全局变量main函数开始之前；静态变量第一次调用fun()创建；

  - 全局变量

    很拧巴，不要用

    语法正确：可见域为本文件，其他文件不可以extern外链接，故没有必要。

  + 函数

    可见域从全局可见改为本文件可见，其他文件不可以extern外链接该函数

  - 类属性 data member

    ```cpp
    class Test
    {
    public:
      static int i; //所有实例共享同一块内存
      int j;
    public:
      Test(int aj); //初始化不能包含静态属性
    }
    
    Test::Test(int aj)
    {
      j = aj;
    }
    
    int Test::i = 100; //静态属性初始化
    
    int main()
    {
      Test t1(2);
      Test t2(3);
      cout << t1.i << endl;
      t1.i = 200;
      cout << t2.i << endl;
    }
    ```

  - 类函数 function member

    没有static，若想访问类方法，必须要创建实例；

    通过static可以直接访问类方法，不需要创建实例。

    ```cpp
    class Test
    {
    public:
      static int i;
      int j;
    public:
      Test(int aj); 
      
      static void test_fun(); //可以通过类名直接调用的函数
    }
    
    Test::Test(int aj)
    {
      j = aj;
    }
    
    int Test::i = 100; 
    
    void Test::test_fun()
    {
      // 静态函数内，不可以写this，故不能操纵非static属性
      i = 10; //只能操纵static属性
    }
    
    int main()
    {
      Test.test_fun();
      t1.test_fun(); //通过实例也可以调用static函数
      return 0;
    }
    ```

+ design pattern 设计模式

  构建型，结构型，行为型

  - 单件模型 singleton pattern

    ```cpp
    class Singleton
    {
    private:
      Singleton();
      static Singleton* self;
    public:
      static Singleton* get_object();
    }
    
    Singleton* Singleton::self = NULL;
    
    Singleton* Singleton:: get_object()
    {
    	if(self == NULL)
        self = new Sigleton();
      return self;
    }
    // 未完善，没有写构造函数，没有delete
    
    ```

    

+ 运算符重载

  ```cpp
  class Account
  {
  private:
    int id;
    int balance;
  public:
    Account();
    void DepositMoney(int m);
  };
  
  void Account::DepositMoney(int m)
  {
    this.balance += m;
  }
  
  Account::Account()
  {
    balance = 0;
  }
  
  int main()
  {
    Account a;
    a.DepositMonet(100);
    return 0;
  }
  ```

  能不能写a = a+100呢？

  ```cpp
  class Account
  {
  private:
    int id;
    int balance;
  public:
    Account();
    Account& operator +(int m); //运算符重载
    Account operator ++(int); //后++，返回对象，pass by value，拷贝构造
    Account* operatpr ++();//前++
  };
  
  Account& Account:: operator +(int m)
  {
    this.balance += m;
    return *this; //返回this地址
  }
  
  Account Account::operator ++(int)
  {
    Account old = *this;
    this->balance++;
    return old; //不能返回*old，否则返回局部变量地址
  }
  
  Account* Account::operator ++()
  {
    this->balance++;
    return *this
  }
  
  ```

  

