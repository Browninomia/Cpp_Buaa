# 多态



+ 向上类型转化 upcasting

  + 向下类型转化 downcasting

    不安全：可能导致越界：4字节对象转换为8字节

  ```cpp
  void fun(Base base) {
  }
  
  int main() {
    Derived d;
    fun(d); //将Derived转化为Base传入
  }
  ```

  + 前绑定：

    因为发生了向上类型转化upcasting，所以就算传入子类，但upcasting后会调用父类的方法。

  ```cpp
  class Pet
  {
    char *name;
    int age;
  public:
    void speak();
  };
  
  void Pet::Speak()
  {
    cout << "Pet Speak" << endl;
  }
  
  class Cat: public Pet
  {
  public:
    void speak();
  };
  void Cat::speak()
  {
    cout << "Meow Meow" << endl;
  }
  
  void Needle(Pet pet)
  {
    pet.speak();
  }
  
  int main()
  {
    Cat cat;
    Needle(cat);
  }
  ```

  + 后绑定/动态绑定：

    实现方法：虚函数 [virtual]

  ```cpp
  class Pet
  {
    char *name;
    int age;
  public:
    virtual void speak();
  }
  
  void Needle(Pet& pet)
  {
    pet.speak(); //多态
  }
  ```

  + 多态是如何实现的？

    Pet对象含有virtual，调用pet.speak的时候会去**<u>寻址</u>**子类的方法

    创建Cat对象，前8字节是指向父类虚函数的实现Cat::speak的指针

    V-table虚函数表，属于类Cat

    V-ptr虚指针，属于Cat对象，指向Cat类的虚函数表

+ 抽象类

  + 纯虚函数

    当一个类只要有一个纯虚函数时，则此类是一个抽象类

  ```cpp
  class Pet
  {
  public:
    virtual void speak() = 0 // 纯虚函数
  }
  ```

  ​		抽象类不允许实例化

  设计**抽象类**（通常称为 ABC）的目的，是为了给其他类提供一个可以继承的适当的基类。抽象类不能被用于实例化对象，它只能作为**接口**使用。如果试图实例化一个抽象类的对象，会导致编译错误。

  因此，如果一个 ABC 的子类需要被实例化，则必须实现每个虚函数，这也意味着 C++ 支持使用 ABC 声明接口。如果没有在派生类中重写纯虚函数，就尝试实例化该类的对象，会导致编译错误。

  可用于实例化对象的类被称为**具体类**。

+ 构造析构的多态性

  + 构造函数

    父类构造函数调用虚函数，不具有多态性：因为构造函数的调用顺序，决定了在调用父类构造函数的时候，子类还没有被构造，因此此时的虚指针指向的是父类的虚函数表。

  + 析构函数

    If the pointer is to the base class, the compiler can only know to call the base-class version of the destructor during delete. Sound familiar? This is the same problem that virtual functions were created to solve for the general case. Fortunately, virtual functions work for destructors as they do for all other functions except constructors.

    在析构的时候，我们不会面临构造函数多态化的困境，因为一旦一个对象被构造，他的虚指针就已初始化，我们就可以调用虚函数了。

    + 纯虚析构函数

      父类的纯虚析构函数必须要写函数体，子类不必实现该纯虚析构函数。

    + 在父类析构函数中调用虚函数

      多态性失效，因为子类已经析构

+ 接口

  多重继承
  
  行为串联

