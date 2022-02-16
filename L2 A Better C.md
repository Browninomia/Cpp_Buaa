# A better C

+ Part I 对函数的改进

  1. Overloading 重载 过载

     cpp可以存在同名函数，只要不同参数即可

     > ``` cpp
     > void func_c (int i) {
     > 
     > }
     > 
     > Void fun_c (char *p) {
     > 
     > }
     > // 不能通过返回值区分同名函数, e.g.
     > // int func
     > // double func
     > ```

  2. 默认参数default parameter

     // parameter 和 argument 的区别?

     > ```cpp
     > void fun1(int a, int b, int c=4) {
     >   // 第三个参数是默认参数，可以减少程序员写参数的个数，不会减少传递参数的个数
     >   // 不可以写成 a, b=3, c，从第一个默认参数开始，后续必须都是默认参数
     > }
     > ```

  3. 占位参数

     > ```cpp
     > void fun3(int)
     > {
     >   //占位参数的功能是为了便于后续新版本软件开发
     >   //然而，更普遍的是用于orverloading。对于两个同名同参函数，通过占位参数对两个函数进行区分
     > }
     > ```

+ Part 2 面向对象

  - C 语言结构体的构造方法

    ```cpp
    struct Person
    {
      int age;
      char *name;
    };
    
    //一定传的是p的地址*p pass by address
    void init_person(Person *p, int aage, char *aname)
    {
      p->age = aage;
      p->name = aname;
      //->和.是一样的，可以连续写，例如p->name.f1->f3.tag = aname;
    }
    
    ```

    Struct 相关数据和相关操作均独立于结构体之外

  - C++ class

    ```cpp
    class Person
    {
    public:
      //属性 数据成员
      int age;
      char *name;
      //封装init，避免了多个struct的init函数重名问题
      //行为 method 函数成员
      void init(int aage, char* aname);
    };
    
    void Person:: init(int aage, char* aname)
    {
      //隐藏了this指针
      age = aage;
      name = aname;
    }
    
    int main(int argc, char* argv[])
    {
      Person p1,p2; //Person是类，p1,p2是对象。类是唯一的，是客观世界的抽象；对象是无穷多的，是客观存在的
      cout << "p1 address: " << &p1 <<endl;
      p1.init(10, (char *)"zhangsan");
      cout << p1.age << p1.name <<endl;
      cout << "p1 address: " << &p2 <<endl;
      p2.init(20, (char *)"lisi");
      cout << p2.age << p2.name <<endl;
    }
  ```
  
- 访问控制 私有&共有
  
  class内部默认私有
  
  struct内部默认公有
  
    ```cpp
    #include <iostream>
    using namespace std;
    
    class Person
    {
    private:
      int age;
      char *name;
  public:
      void init(int aage, char* aname);
      int get_age() {return age;}
    };
    
    int main() {
      Person p;
      cout << p.get_age()<<endl;
      int *p1 = (int*)&p; //指针直接操作内存，可以访问私有成员
      *p1 = 10; // 私有属性p1.age被设定为10
    cout << p.get_age()<<endl;
      return 0;
  }
    ```

    我们可以把类的定义隐藏起来，不知道类的data number，就无法通过指针设定私有属性了。
  
  - 初始化：构造函数、构造器
  
    ```cpp
    class Person
    {
    private:
      int age;
      char *name;
    public:
      void init(int aage, char* aname);
      // constructor should be overloaded 构造函数、构造器
      Person(int aage, char *aname);
      Person(int aage); //overload
    	//default constructor
      Person(); //将默认构造补回来了
    };
    
    Person::Person(int aage, char *aname) {
      age = aage;
    this->name = *aname;
      cout << "constructor" << endl;
  };
    ```

    1. constructor should be overloaded 
  
    2. Default constructor: 在类的内部总是存在一个默认构造，无参数无返回值，直到写出一个构造函数为止（不存在了）。
  
    ```cpp
    int main() {
      Person *p1 = new Person();
    Person p2();//this is a function
      Person p2;// 创建默认构造的对象不能加括号
  }
    ```
  
    3. 用默认参数代替overloading
  
    ```cpp
    class Test {
      int i;
      int j;
    public:
      Test(int ai, int aj);
      Test(int ai);
    
      Test(int ai, int aj=0);//理论上应该用overloading，这种用默认参数代替的方法也可以使用
    }
    ```
  
    