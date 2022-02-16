# Program's Space

+ 代码区：read only，二进制代码

+ 全局变量区/静态变量区

  ```cpp
  class Person {
    int age;
    char* name;
  public:
    Person(int aage, char* aname);
  }
  Person::Person(int aage, char* aname){
      age=aage;
      name=*aname;
      cout << "constructor" << endl;
  }
  Person global_person(29,"Zhang");
  int main(){
    cout << "******" << endl;
    return 0;
  }
  ```

  全局变量在main函数之前执行（长生不老）

+ 常量区：局部变量/全局变量的值

+ 栈区：局部变量名

+ 堆区：new或malloc的对象

+ Runtime memory

  内存分配：

  | 数据结构 | 速度         | 变量消亡                                   | 场景             |
  | -------- | ------------ | ------------------------------------------ | ---------------- |
  | 堆heap   | 慢，寻址问题 | 可控                                       | 面对未知数据大小 |
  | 栈stack  | 快，连续的   | 局部变量，先创建后消亡（不可控，自动消亡） |                  |

  栈

  ```cpp
  int* fun() {
    int a=10;
    return &a;
  }
  int main() {
    int *p=fun(); //指向局部变量地址，但是局部变量已经消亡了
    cout << p << endl; // 0
    return 0;
  }
  ```

  堆

  ```cpp
  int* fun() {
    int *p = (int*)malloc(sizeof(int));
    *p = 10;
    return p;
  }
  int main() {
    // memory leak 内存泄漏，需要记得还内存
    int *p=fun(); //指向局部变量地址，但是局部变量已经消亡了
    cout << p << endl;
    cout << *p << endl; // 10
    return 0;
  }
  ```

  Java 自动内存回收garbage collector，堆聚，速度慢。

  C需要变量使用者记得回收

  

  上述例子返回的是一个地址，事实上：

  **函数返回值基本上是void，参数传地址，参数既可以是input也可以是output**

  **return value不用考虑了**

+ value or addres

  ```cpp
  class Test {
    int i; //value
    int *j; //address
  }
  ```

  calss的组成部分：

  若是必然组成部分，就写value，

  如果有时候有，有时候没有（大部分时候value为空），就不要写value，写address，不应该占用必然内存，

  指针只有4字节，比放一大段vlaue节省空间。

+ 嵌套定义

  ```cpp
  class Circle {
    Point p; //Point未定义
  };
  class Point {
    Circle c;
  };
  ```

  修改：传指针

  ```cpp
  class Point;
  class Circle {
    Point *p;
  };
  class Point {
    Circle c;
  };
  ```

  编译需要确定类的大小，传指针就声明了空间大小，4字节。

+ memory leak & 析构函数

  ```cpp
  class Test {
    int i; //value
    int *j; //address
  public Test(int i, int* j);
  }
  Test::Test(int ai, int aj) {
    i=ai;
    j=(int*)malloc(sizeof(int));
    *j=aj;
  }
  int main() {
    Test(1,2);
    return 0;
  }
  ```

  Test为局部变量，在栈里，占用8字节，*j指向堆，占用4字节，与Test相关的空间公有12字节。

  Test局部变量自动消亡，但是堆里的空间未被释放，存在内存泄漏

  解决方法：析构

  ```cpp
  Test::~Test(){
    cout << "discontruct" << endl;
    free(j);
  }
  ```

  析构不存在overloading，没有参数。

  ```cpp
  Test::Test(int ai) {
    i=ai;
    j=NULL;
  }
  ```

  没有j的时候，需要赋值为空，否则j是垃圾无法free。

+ 大型操作系统有保护机制，如果有内存泄漏会自动还

  1. 泄露一般是感受不到的

  2. 不能重复释放，会崩，因此不直接写free(p)

     ```cpp
     if(p != NULL) {
       free(p);
       p = NULL;
     }
     ```

  
  类内所有(包括构造函数与方法)分配的堆聚空间，析构中一定是方。

+ reference 引用

  ```cpp
  int main()
  {
    int *p; //fly pointer 野指针
    int i=10;
    int j=20;
    p = &i;
    p = &j; //随意乱指
    
    //reference 引用
    int& r=i;// must initilize; 指针可以不初始化，引用必须初始化，成为某个变量的引用，且永远是那个变量的引用(安全)
    
  }
  ```

  