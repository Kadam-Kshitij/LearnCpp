# Mutable keyword
```cpp
// Mutable keyword in C++
// Used to modify a member variable inside a const member function
class Base {
    mutable int val{ 67 };
public:
    void foo() const
    {
        val = 2;
    }

    void print() const
    {
        std::cout << val << std::endl;
    }
};

int main()
{
    const Base bobj;
    bobj.foo();
    bobj.print();   // 2
}
```

# Handeling new operator fails -
1) Use nothrow version of new - If new fails, ptr will contain nullptr.<br>
```cpp
int main()
{
    int* ptr = new ( std::nothrow ) int[0x7ffffffff];
    if( nullptr == ptr )
    {
        std::cout << "New operator failed!!!\n";
        return 0;
    }

    // std::cout << *ptr << std::endl;
    delete[] ptr;
}
```
2) Use try catch block
```cpp
int main()
{
    try
    {
        int* ptr = new int[0x7ffffffff];
        delete[] ptr;
    }
    catch( const std::bad_alloc& ex )
    {
        std::cout << ex.what(); // std::bad_alloc
    }
}
```
# Using new with free
```cpp
class Base {
public:
    Base() { std::cout << "Base\n"; }
    ~Base() { std::cout << "~Base\n"; }
};

int main()
{
    Base* ptr = new Base();
    free( ptr ); // Destructor not called
    // delete ptr;  // Destructor called
}
```
# Singleton Instance
```cpp
#include <mutex>

std::mutex mu;

class Singleton {
    Singleton() {};
public:
    Singleton( const Singleton& s ) = delete;
    Singleton& operator =( const Singleton& s ) = delete;

    static Singleton* instance;
    static Singleton* getInstance();
    static void deleteInstance();
};

Singleton* Singleton::instance{ nullptr };

Singleton* Singleton::getInstance()
{
    std::lock_guard< std::mutex > lock( mu );
    if( !instance )
    {
        instance = new Singleton();
    }
    return instance;
}

void Singleton::deleteInstance()
{
    std::lock_guard< std::mutex > lock( mu );
    delete instance;
}

int main()
{
    Singleton* sig = Singleton::getInstance();
}
```
# Prevent heap allocated object creation
```cpp
// Make new operator private to prevent dynamic allocation
class Base {
    void* operator new( size_t count )
    {
    }
public:
};

int main()
{
    // Base* obj = new Base();  // Not allowed
    Base obj;
}
```
# Prevent object copy
Make copy constructor and copy assignment operator private.<br>
```cpp
class Base
{
    Base( const Base& obj ) = default;
    Base& operator=( const Base& obj ) = default;
public:
    Base() = default;
};

int main()
{
    Base obj1;
    // Base obj2( obj1 );   // Not allowed
    Base obj2;
    // obj2 = obj1;         // Not allowed
    Base obj3{};
}
```

# Prevent class inheritance
1) Use final keyword
```cpp
class Base final
{
};

// CTE 
class Derived : public Base {};
```
2) Make the Base class constructor private and create a static function which can generate and provide Base class instance.<br>
```cpp
class Base
{
    Base() { std::cout << "Base\n"; }
public:
    static Base* instance;
    static Base* getInstance()
    {
        return new Base();
    }
};
Base* Base::instance{ nullptr };

// CTE
class Derived : public Base
{
};

int main()
{
    Base* ptr = Base::getInstance();    // Ok
    Derived obj;    // CTE
}
```
# Calling C function from C++ code
```cpp
#ifdef __cplusplus
extern "C"
{
#endif
     //some functions here
     //functionOne();
    //functionsTwo();

#ifdef __cplusplus
}
#endif
```
