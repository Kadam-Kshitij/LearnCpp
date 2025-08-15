# Mutable keyword
```cpp
// Mutable keyword in C++
// Used to modify a member variable inside a const member function
#include <iostream>

class Base {
    mutable int x{ 78 };
    int y { 99 };
public:
    void foo() const
    {
        ++x;
        std::cout << x << std::endl;    // 79
        // ++y;				// CTE - Cannot modify y inside a const member function
        std::cout << y << std::endl;    // 99
    }
};

int main()
{
    Base obj;
    obj.foo();
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
a.c is compiled using command `gcc -o a.o -c a.c -fPIC` and `gcc -o libmy.so a.o -shared -fPIC`
```cpp
#include <stdio.h>

void foo()
{
    printf( "Foo\n" );
}
```
main.cpp is compiled using command `g++ -o b main.cpp -L. -lmy`
```cpp
#include <iostream>
// If extern "C" is not used will get undefined reference error
extern "C"
{
	void foo();
}

int main()
{
	foo();
}
```
# Name Mangling
Name mangling in C++, means, compiler gives different names to the overloaded functions to avoid ambiguity during functions call at compile time.<br>

# When to use Dynamic cast
When we want to call a Derived class function which is not available in Base class.<br>
```cpp
class Base {
public:
    virtual void foo() { std::cout << "Foo\n"; }
};

class Derived : public Base
{
public:
    void foo() override { std::cout << "Foo Derived\n"; }
    void goo() { std::cout << "Goo Derived\n"; }
};

int main()
{
    Base* ptr = new Derived();

    Derived* dptr = dynamic_cast< Derived* >( ptr );
    if( nullptr == dptr )
    {
        std::cout << "Cast failed\n";
        return 0;
    }

    dptr->goo();
}
```
# Overload new and delete operator
```cpp
class Base {
    int val{};
    double vald{};
public:
    Base(){ std::cout << "Base()" << std::endl; }
    Base( int x, double y ) : val{ x }, vald{ y }
    {
        std::cout << "Base" << std::endl;
    }
    ~Base()
    {
        std::cout << "~Base" << std::endl;
    }

    void* operator new( size_t count )
    {
        std::cout << "New : " << count << std::endl;
        void* rptr = malloc( count );
        if( NULL == rptr )
        {
            throw std::bad_alloc();
        }
        return rptr;
    }

    void operator delete( void* ptr )
    {
        std::cout << "Delete" << std::endl;
        free( ptr );
    }

    void* operator new[]( size_t count )
    {
        std::cout << "New : " << count << std::endl;
        void* rptr = malloc( count );
        if( NULL == rptr )
        {
            throw std::bad_alloc();
        }
        return rptr;
    }

    void operator delete[]( void* ptr )
    {
        std::cout << "Delete" << std::endl;
        free( ptr );
    }

    void print() const
    {
        std::cout << val << ", " << vald << std::endl;
    }
};

int main()
{
    Base* obj = new Base( 34, 56.5 );
    obj->print();
    delete obj;
    std::cout << "----\n";

    Base* objarr = new Base[2];
    objarr[0].print();
    delete[] objarr;
}

//New : 16
//Base
//34, 56.5
//~Base
//Delete
//----
//New : 40
//Base()
//Base()
//0, 0
//~Base
//~Base
//Delete
```

# Extra Questions
```
int* ptr = new int{ 90 };
```
Here ptr is allocated on the stack, and points to a memory location on the heap.

