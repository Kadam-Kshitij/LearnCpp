# Compilation Process

```
.cpp files<br>
    |
    |
    |   Pre-processing ( include files, remove comments, expand macros )
    |
    |
   \ /
    '
.ii files
    |
    |
    |   Compiler ( Complie code and convert to assembly language code )
    |
    |
   \ /
    '
.s files ( assembly language )
    |
    |
    |   Assembler ( Convert to machine language object files )
    |
    |
   \ /
    '
.o files ( object files )
    |
    |
    |   Link different object files/ library files together
    |
    |
   \ /
    '
Executable
```

# Chapter 1
## Standard I/O
std::cout has a buffer which is flushed periodically.<br>
endl vs "\n"<br>
Both causes the output to new line. But endl also flushes the cout buffer which takes time.<br>
C++ periodically flushes after some time. So use \n to increse code speed.<br>
"\n" also occupy 1 byte of memory<br>

# Chapter 2
## Header Guards
Used to prevent including same header file twice which can cause CTE

```cpp
#ifndef ___UNIQUE_NAME___
#define ___UNIQUE_NAME___
...
#endif // ___UNIQUE_NAME___

Another way is by using
#pragma ones
```

# Chapter 8
## Switch statement
If break/return statements are not used in case statement, the next case statements will be executed as well.<br>
If there is a initialiation ( int y = 90; ) in a case statement it will throw CTE. Except if it is within a scope { }<br>
Defination and assignment is allowed. ( int y; y = 10; )<br>

## Program termination ----->
1) std::abort() - Abnormal termination without cleanup
2) std::exit( EXIT_FAILURE ) - Normal termination with cleanup
3) std::quick_exit( EXIT_SUCCESS ) - Normal termination without complete cleanup
4) std::_Exit( EXIT_SUCCESS ) - Normal termination without cleanup
5) std::atexit( <function_name> ) - Register function to be called when exit is called. In case of multiple function, order of calling is reverse of adding them.
6) std::at_quick_exit( <function_name> ) - Register function to be called when std::quick_exit() is called.

## Random number generation 
Random number between [91,120]<br>
`int random = rand() % 30 + 91;`

# Chapter 11 - Function Overloading

```cpp
void foo( float f )
{
    std::cout << "Float\n";
}

void foo( double f )
{
    std::cout << "Double\n";
}

int main()
{
   foo( 12.3 );     // Double
   foo( 12.3f );    // Float
}
```

Functions can be overloaded using type of parameter, number of parameters. But not by return type.<br>
Following is an ambigious match

```cpp
void foo( int i )
void foo( const int& i )
```

# Chapter 12 - Reference and Poiters
## Reference
lvalue - An expression which has a specific memory location.<br>
lvalue reference - Reference to an lvalue.<br>
lvalue reference needs to be initialized.<br>
Reference ones initialized cannot refer to another expression<br>
What will happen when address of reference is added ???<br>
Lvalue references can only bind to modifiable lvalues.
const lvalue reference can bind to modifiable lvalue, non modifiable lvalue and rvalues.<br>
bind a temporary object to a const lvalue reference increases the lifespan of temporary object to match lefespan of the refernce.<br>
Lvalue references to const can even bind to values of a different type, so long as those values can be implicitly converted to the reference type. Binds to temporary object.<br>
The same applies when passing as reference to a function.<br>
TODO - string_view, constexpr reference.

```cpp
    int& ref;			// CTE
    int& ref = a;		// Ok

    const int x = 89;
    int& ref = x;		// CTE
    const int& ref = x;		// OK

    int& ref{ 4 };		// CTE - Cannot bind to a rvalue

    int b = 34;
    int& ref = b;
    ref = a;			// Here b will be assigned value of a

    const int& ref{ 34 };	// Ok - const lvalue reference can bind to rvalue
    std::cout << ref << std::endl;	// 34

    const double& r1 { 5 }; 	// Ok, Binds to temporary object

    char ch = 'a';
    const int& ref{ ch };   	// Ok - ref is bound to temporary object.
    ch = '+';
    std::cout << ref << std::endl;	// Prints 97
```

## Pointers
Multiple pointers can be declared on the same line `int* ptr1, * ptr2, * ptr3;`
Pointer which is not initialized is called wild pointer. Dereferencing wild pointer leads to undefined behavior.<br>
Pointers which have been deleted are known as dangling pointers. Dereferencing a dangling pointer leads to undefined behaviour.<br>
Size of pointer - 4 bytes on 32 bit machine and 8 bytes on 64 bit machine.<br>
Null pointers are pointer which point to nothing. Dereferencing a null pointer leads to undefined behavior.<br>

## const Pointers
```cpp
Const Pointers ----->
int x = 10;
int y = 20;

const int* ptr1 = &x;	// Pointer to const data
// *ptr1 = 20;	// CTE
ptr1 = &y;		// Ok

int* const ptr2 = &x;	// Const pointer
// ptr2 = &y;	// CTE
*ptr2 = 30;		// OK

const int* const ptr3 = &x;	// Const pointer to const data
// *ptr3 = 30;	// CTE
// ptr3 = &y;	// CTE

const int z = 30;
const int* ptr4 = &z;
// int* ptr5 = &z;			// CTE - invalid conversion from ‘const int*’ to ‘int*’
// int* const ptr6 = &z;	// CTE - invalid conversion from ‘const int*’ to ‘int*’
```

nullptr has type std::nullptr_t (defined in header <cstddef>).<br>


# Chapter 14 - Introduction to Classes

In C struct cannot have member functions. <br>
In C++ struct can contain both member functions and member variables.<br>
<br>
## Const Objects
`const Base obj;`<br>
Ones created, we cannot modify the member variables of const object. Member variables can be initialized in constructor body/ initializer list<br>
const object cannot call member functions which can modify the member variables.<br>
const objects can call only const member functions `void foo() const {}`<br>
Non-const objects can call both const and non-const member functions.<br>
If a member function does not modify the member variables but is not defined as const, then const objects cannot access the function.<br>
<br>
It is possible to overload two functions with same name and signature but dirrerent constness.<br>
Const object will call the const function and non const objects will call the non const function.<br>
<br>
## Access Specifiers
The members of struct are public by default.<br>
The members of class are private by default.<br>
Struct inherit publicly while class inherit privately.<br>

| Access specifier | Scope |
|------------------|--------------------|
| Public | Can be accessed from anywhere |
| Private | Can be accessed within the class and in friend functions/class |
| Protected | Within the class, friend functions/class and in derived classes |

Data members can be returned by const lvalue reference to prevent expensive copying. The lifetime of a data member will be equal to lifetime of the object.<br>
`const int& foo(){ return m_x; } // m_x is a data member `<br>
`int& foo(){ return m_x; } // In this case if calling function modifies the reference, the actual class variable will be modified`<br>
const member functions cannot return non-const reference to data members.<br>
<br>
If class has public member functions,

```cpp
class Base{
public:
	int x, y;
};
```

Then we can use aggregate initialization ` Base obj{ 34, 56 }; ` to initialize x and y.<br>
This will not work if x, y were private. Making data member private makes the class a non-aggregate.<br>
<br>
## Member initializer list
`Derived( const int& x, const int& y ) : Base{ x }, m_y{ y }`<br>
Uses<br>
1) Call base constructors<br>
2) Initialize reference and const member variables.<br>
3) Increases speed - Variables are initialized directly rather than default initialization and then assigning values in constructor body.<br>
<br>
Order of initialization happens in order in which they are defined in class, and not in order in which they are defined in member initialization list.<br>

```
class Base
{
    int y;
    int x;
public:
    Base( const int& valx, const int& valy ) : x{ valx }, y{ x }
    {
    }
};
```

In this case y will be assigned first a garbage value of x, and then x will be assigned valx value. This will cause problem.<br>
<br>
## Constructor
Default constructor - Has no parameters. `Base() {}` If no default constructor is provided by user, the compiler providees one.<br>
If any type of constructor is specified by user, default constructor is not provided by compiler.<br>

We can tell the compiler to provide a default constructor. This is usefull when we have a user defined constructor but still need the default constructor<br>

```cpp
Base() = default; // generates an explicitly defaulted default constructor
```

Constructors can have default values

```cpp
Base( int x, int y = 0 )
{
}
```

Delegating constructors - One constructor can call another constructor via the initializer list<br>

```cpp
Base( int x ) : Base{ x, 0 }
{
}
Base( int x, int y ) : m_x{ x }, m_y{ y }
{
}
```

A constructor which delegates( calls another constructor ) is not allowed to do any initialization.<br>
One constructor may call another constructor and that constructor can again call the first. Leading to a infinite loop and stack may get filled up.<br>

Constructors can be overloaded.<br>
During constructor call, implicit conversions can happen.

```cpp
Base( int a, int b )	// Constructor
Base obj( true, 'a' );	// Call
```

## Copy constructor
Used to create a new object from a already existing object.
`Base( const Base& obj ){}`<br>
const is not mandatory, but should be used to avoid accidentally modifying the paramter. & is mandatory. If not used will cause CTE.<br>
Compiler provides a copy constructor if not provided by user.<br>
We can request for a compiler provided copy constructor using<br>

```cpp
Base( const Base& obj ) = default;
```

If we dont want to create copies, we can delete the copy constructor.<br>

```cpp
Base( const Base& obj ) = delete;
```

<br>
Uses<br>
1) Function call with pass by value. When constructor is passed by value to a function, the arg is copied into the parameter using copy constructor.<br>
2) Function return with pass by value 		****<br>
3) Creating new object from already existing object.<br>

## Explicit keyword
Prevent implicit conversions from different types to class type<br>
Suppose a function takes class object by value as parameter, but we send int as argument during function call,<br>
then the int will get converted to class object.<br>
Similarly a function is returning class object by value, but the function is actually returing int,<br>
then implicit conversion from int to class will happen.<br>
If explicit keyword is used, then such implicit conversions are not allowed.<br>

# Chapter 15 - More on classes
## this pointer
this pointer points to the object which called the class member variable.<br>
It is of the same type as the object it is pointing to and is const. ( Base* const )<br>
<br>
If we print<br>
&obj in main or<br>
this in class member function, it will print the same address which is the address of the class object.<br>
<br>
It is sent internally by the compiler as an argument to the class member function.<br>
<br>
this pointer is not availabe inside the static members since, static members are not associated to the object but to the class.<br>
<br>
this pointer is available inside the constructor as well. The object is created and then<br>
<br>
It is a rvalue, not a lvalue. So we cannot print its address like &this<br>
or change its pointing location as this = new Base;<br>
<br>
this is not available inside friend function<br>
<br>
sizeof is size of pointer<br>
<br>
delete this in the destructor will call the destructor infinite number of times.<br>
delete can be used only with objects created with new. Otherwise the output is undefined behavior. ( invalid pointer )<br>
<br>
delete in constructor or function will cause the memory to be deleted. So the values at that location will be garbage values.<br>
And double free error will occur when the destructor is called.<br>
<br>
Used to Differentiate between member variable and function argument which have the same name.<br>
this->x = x;<br>
For non-const member functions, this is a const pointer to a non-const value (meaning this cannot be pointed at something else, but the object pointing to may be modified).<br>
With const member functions, this is a const pointer to a const value (meaning the pointer cannot be pointed at something else, nor may the object being pointed to be modified).<br>

## Friend
Friend function has access to private and protected members of class as if it is a member function of the class.<br>
Function can be friend to multiple class at a time.<br>
A friend class is a class that can access the private and protected members of another class.<br>
We can also make only single member function of a class as friend of another class.

```cpp
	friend class Base;
	friend void foo( const Base& obj );
	friend void Base::foo( const Base& obj );
```

```cpp
class Base
{
    int a{ 76 };
public:
    friend void print( const Base& obj );
};

void print( const Base& obj )
{
    std::cout << obj.a << std::endl;    // Has access to private member of Base
}

int main()
{
    Base obj;
    print( obj );
}
```

```cpp
class Base;

class Friend2
{
public:
    void foo( const Base& obj );
};

class Base
{
    int a{ 34 };
public:
    friend class Friend;
    friend void Friend2::foo( const Base& obj );
};

class Friend
{
public:
    void foo( const Base& obj )
    {
        std::cout << obj.a << std::endl;
    }
};

void Friend2::foo( const Base& obj )
{
    std::cout << obj.a << std::endl;
}

int main()
{
    Base objb;
    Friend objf;
    objf.foo( objb );

    Friend2 objf2;
    objf2.foo( objb );
}
```

# Static member variables
static members are only declared inside the class.
They need to be explicitly defined outside the class.

Static member variables are shared accross all objects of the class.<br>
Static members are not associated with the class objects. Rather they are with the class.<br>
They are created at the start of program and destroyed when the program ends.<br>
They can be accessed even when class object is not created.<br>
They can be directly accessed using class name and scope resolution operator `std::cout << Base::var << std::endl;`
When we declare a static member variable inside a class type, we’re telling the compiler about the existence of a static member variable, but not actually defining it (much like a forward declaration). Because static member variables are essentially global variables, you must explicitly define (and optionally initialize) the static member outside of the class, in the global scope.

```cpp
class Base {
public:
    static int x;
};
int Base::x = 23;
```

Class cannot contain data member as its own type

```cpp
class A{
    A a;
};
```

This will give CTE - incomplete type. But it can contain static A a or A* a;<br>

`static const int s_value{ 4 };` A static const int can be defined and initialized directly.<br>
Static members are defined in the cpp file and not in the h file of the class to prevent multiple copies.<br>



# Chapter 19 - Dynamic memory allocation
```cpp
int* ptr = new int{ 34 };	// Allocate memory for int
delete ptr;			// Delete allocated memory
```

## Dangling pointer
Pointer pointing to a deallocated memory is know as dangling pointer
Dereferencing a dangling pointer leads to undefined behavior.

```cpp
int main()
{
    int* ptr = new int{ 56 };
    std::cout << *ptr << std::endl; // 56
    delete ptr;

    // Here ptr is a dangling pointer
    std::cout << *ptr << std::endl; // 0
}
```

Set deleted pointers to nullptr unless they are going out of scope immediately

```cpp
int main()
{
    int* ptr = new int{ 56 };
    std::cout << *ptr << std::endl; // 56
    delete ptr;
    ptr = nullptr;
    // Further statements
}
```

## new operator and std::nothrow
Allocating memory using new might fail in rare circumstances and will lead to bad_alloc exception being thrown. If exception is not hadled properly will lead to crash.<br>
We can add std::nothrow between new and allocation type. This will ensure no exception is thrown, and nullptr will be returned instead of address.

```cpp
    int* ptr = new ( std::nothrow )int{ 56 };
    if( !ptr )
    {
        // Error handling
    }
```

## Null pointers
Pointer is not allocated to any memory location. Dereferencing a null pointer will lead to crash.<br>
Deleting a null pointer causes nothing.<br>

## Dynamically allocating arrays
Use `int* ptr = new int[4];` to dynamically allocate array of memory.<br>
Use `delete[] ptr;` to delete array of memory.<br>

## Destructor
1) Has the same name as class with ~ symbol at the start.
2) Does not take any arguments.
3) No return type.
4) Class can have only one destructor. No Overloading possible.
5) Automaically called when object of the class is deleted or goes out of scope.
6) Destructors can call member functions.

## Pointers to pointers

```cpp
    int* ptr = new int{ 6 };
    int** pptr = &ptr;
    std::cout << **pptr << std::endl;
```

Two dimentional array

```cpp
int main()
{
    int row = 4, column = 3;

    int** ptr = new int*[row];
    for( int i = 0; i < row; ++i )
    {
        ptr[i] = new int[column];
        for( int j = 0; j < column; ++j )
        {
            ptr[i][j] = i + j;
        }
    }

    for( int i = 0; i < row; ++i )
    {
        for( int j = 0; j < column; ++j )
        {
            std::cout << ptr[i][j] << ", ";
        }
        std::cout << std::endl;
    }

    for( int i = 0; i < row; ++i )
    {
        delete[] ptr[i];
    }
    delete[] ptr;
}
```
Instead we can just use one dimentional array and using simple maths perform operations
`int* ptr{ new int[4*3] }`

## Void pointers
Void pointers can point to any data type. But needs to be first cast into appropriate pointer type before dereferencing.<br>
Void pointers cannot be dereferenced. Cannot perform arithmattic operations on it. Deleting void pointer will lead to undefined behavior.<br>

```cpp
    void* ptr;
    int a = 23;
    ptr = &a;   // Ok
    // Need to cast before dereferencing.
    std::cout << *static_cast< int* >( ptr ) << std::endl;
```

# Chapter 24 - Inheritance
## Order of constructor call
Order of constructor call - Most Base class constructor is called first follwed by derived class.<br>
Base <- D1 <- D2 <- D3<br>
In the above case order of constructor call is Base, D1, D2, D3.

In case of Multiple inheritance
1) class Derived : public Base2, public Base1<br>
   In this case constructor call order is Base2, Base1, Derived  
2) class Derived : public Base1, public Base2<br>
   In this case constructor call order is Base1, Base2, Derived

Order of destructor is exactly opposite to the constructor call.<br>

## Initializing Base class from derived class
Initializer list can be used to call Base class constructors<br>
`Base( int a ) : _a{ a }`
`Derived( int a, int b ) : Base{ a }, _b{ b }`

## Access specifiers and inheritance
Depending upon the type of inheritance ( public, protected, private ) the access of base class members changes in the derived class.<br>
Following table summarizes the same

| Type of inheritance | public ( in Base class )	|	protected ( in Base class ) |	private ( in Base class ) |
|-------------------|-------------|---------------|-------|
|public |            public |      protected |  X |
|protected |         protected |   protected |  X |
|private |           private |     private |    X |

## Adding new functionality in Derived class
If we have same name function in Base and derived class, then we can use
Base::foo() in derived class to call the Base class function.

```cpp
void Derived::foo()
{
    // Some additional functionality
    Base::foo();	// Calls the base class foo function
}
```

To call Base class function using the Derived class object we can use,

```cpp
Derived obj;
obj.Base::foo();         // Calls Base foo
obj.foo();		 // Calls Derived foo
```

## Changing inherited members access
```cpp
public:
	using Base::foo;
```

This will make foo public in Derived class which might have been private/protected in Base<br>

Example
```cpp
class Base
{
public:
    void foo()
    {
        std::cout << "base\n";
    }
    void goo()
    {
        std::cout << "goo\n";
    }
};

class Derived : public Base
{
private:
    void foo()
    {
        std::cout << "derived\n";
    }
};

int main()
{
    Derived obj;
    // obj.foo();       // CTE - foo is private
    obj.Base::foo();   // OK base
    obj.goo();          // goo
}
```

Example - First derived class will be checked for function foo. If found the base class functions will not be considered

```cpp
class Base
{
public:
    void foo( int a )
    {
        std::cout << "foo base\n";
    }
    void foo( double a )
    {
        std::cout << "foo double\n";
    }
};

class Derived : public Base
{
public:
    void foo( double a )
    {
        std::cout << "foo derived\n";
    }
};


int main()
{
    Derived obj;
    int a = 3;
    obj.foo( a );   // Calls the derived class foo
}
```

Example - by adding using Base::foo; we can make the base class foo available in Derived class for overloading

```cpp
class Base
{
public:
    void foo( int a )
    {
        std::cout << "foo base\n";
    }
    void foo( double a )
    {
        std::cout << "foo double\n";
    }
};

class Derived : public Base
{
public:
    using Base::foo;	// This will make base class functions available in Derived class for overloading
    void foo( double a )
    {
        std::cout << "foo derived\n";
    }
};


int main()
{
    Derived obj;
    int a = 3;
    obj.foo( a );       // Calls the base class foo
    obj.foo( 3.8f );    // Calls the derived class foo
}
```

Similarly we can change the access of public and protected Base class members in Derived class. Private base class members cannot be accessed in Derived class.

```cpp
class Base
{
public:
    void foo()
    {
        std::cout << "foo base\n";
    }

protected:
    void goo()
    {
        std::cout << "goo base\n";
    }
};

class Derived : public Base
{
public:
    using Base::goo;    // This will make goo public in Derived class

private:
    using Base::foo;	// This will make foo private in Derived class
};

int main()
{
    Derived obj;
    obj.goo();      // Ok, goo is public in derived class
    // obj.foo();   // CTE - foo is private in derived class

    Base& bobj = obj;
    bobj.foo();     // Ok, foo is public in base class
    // bobj.goo();  // CTE - goo is protected in base class

    // Second Way
    obj.Base::foo();    // Ok
}
```

## Delete function in derived class
We can also delete base class member in derived class by writting `void foo() const = delete;`<br>
But we can call the base class function as, `objD.Base::foo();` Or we can upcast to Base& and call the foo function in Base in both the above cases.

## Multiple Inheritance
If both base class have the same named function, call will be ambigious leading to CTE.<br>
This can be solved using Base class name with scope resolution operator. But the complexily will increase with increase in inheritance levels.<br>

```cpp
class Base1
{
public:
    void foo()
    {
        std::cout << "foo base1\n";
    }
};

class Base2
{
public:
    void foo()
    {
        std::cout << "foo base2\n";
    }
};

class Derived : public Base1, public Base2
{
};


int main()
{
    Derived obj;
    obj.foo();	// CTE - ambigious call to foo
}
```

## Diamond problem
B1<-B2 , B1<-B3 , B2<-D, B3<-D.<br>
B1 constructor will be called twice due to B2 and B3.<br>
To prevent this make B2 and B3 virtual as follows<br>
	class B2 : virtual public B<br>
	class B3 : virtual public B<br>
	class D : public B2, public B3<br>
Now D can directly call class B1 constructor<br>
	D( int x ) : B1{x}, B2{x}, B3{x} {}<br>

 # Chapter 26
Class template specialization with type and non-type parameters.<br>
The non-type parameter should be a constexpr.<br>
Non-type parameter can be one of the following - <br>
1) Int
2) Enum
3) Pointer or ref to class object
4) Pointer or ref to function
5) Pointer or ref to class member function
6) std::nullptr_t
7) Float since C++20

 ```cpp
template< typename T, int size >
class Container {
    T arr[size];
public:
    T& operator[]( int index );
};

template< typename T, int size >
T& Container<T,size>::operator[]( int index )
{
    return arr[index];
}

int main()
{
    // int a =3;
    // const int b = a; // Not Ok

    // const int b = 4; // Ok

    constexpr int s = 12;

    Container< int, s > obj;
    for( int i = 0; i < s; ++i )
    {
        obj[i] = i;
    }

    for( int i = 0; i < s; ++i )
    {
        std::cout << obj[i] << ", ";
    }
}
```

Template function specialization example

```cpp
template< typename T >
T foo( T x )
{
    std::cout << "foo " << x <<std::endl;
    return x + 2;
}

template<>
std::string foo< std::string >( std::string x )
{
    std::cout << "foo " << x <<std::endl;
    return x + " two";
}

int main()
{
    int ret = foo< int >( 9 );
    std::cout << "ret = " << ret << std::endl;

    std::string retstr = foo< std::string >( "Hello" );
    std::cout << "retstr = " << retstr << std::endl;
}

//foo 9
//ret = 11
//foo Hello
//retstr = Hello two
```
