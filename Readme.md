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

```cpp
int main()
{
    std::cout << "hello" << std::endl;  // Prints immediately
    std::cout << "hello\n";             // Prints after 2 seconds
    sleep(2);
    std::cout << "hello\n" << std::flush;   // Prints after 2 seconds
    std::cout << "hello\n";             // Prints after 4 seconds
    sleep(2);
    std::cout << "hello\n";             // Prints after 4 seconds
}
```
## Namesapce
 A namespace is a container for organizing code into logical groups and preventing name conflicts.<br>
 An anonymous namespace is a namespace with no name. Members of an anonymous namespace can only be accessed within the same source file.<br>
 ```cpp
namespace A {
void foo()
{
    std::cout << "A\n";
}
}

namespace B {
namespace C {   // nested namespace
void foo()
{
    std::cout << "B\n";
}
}
}

int main()
{
    A::foo();   // A
    B::C::foo();   // B
}
```

## Avoid using namespace std
if `using namespace std` is used at the start of the file and<br>
If we define a function named cout in our file and then use it wihout std::, compiler will not know which cout to use.\n
Also a variable defined today might clash with a new variable defined later in the std namespace.\n
```cpp
using namespace std;

void cout()
{
    std::cout << "A\n";
}


int main()
{
    cout(); // Ambigious call
}
```

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

## Preprocessor directives

```cpp
#include <abc.h>
#define ABC	// Object like macros without substitution text. Used for conditional compilation
#define ABC "def"	// Object like macros with substitution text. Used to replace text

#ifdef, #ifndef, #else, #elif #endif	// Conditional compilation
#if 0	// Dont compile
#if 1 	// Compile
#undef <macro> // Undefine a macro
```

Macro text gets replaced in the code where ever used during preprocessing.<br>
Scope of defines - Point where they are defined in the file till the end of file.<br>

```cpp
#define FOO "9"     // const char*
#define GOO 9       // int
#define myStr std::string

int main()
{
    std::string str = FOO;	
    std::cout << str << "\n";

    int x = GOO;
    std::cout << str << "\n";

    myStr s{ "hello World" };
    std::cout << s << "\n";
}
```

```cpp
void foo()
{
    // Ok because macros are processed by preprocessor.
    // Preprocessor does not understand C++ concepts.
#define FOO "Alex"
}


int main()
{
    std::cout << FOO << std::endl;
}
```

TODO : Function like macros
#define MAX(a,b) ((a)>(b)?(a):(b))

## Forward declaration
```cpp
// Forward declare
void foo( int a );
class Base;

int main()
{
    foo( 34 );
    Base* ptr{ nullptr };
}

void foo( int a )
{
    std::cout << "Foo" << a << "\n";
}

class Base {
};
```

```cpp
foo( a, b );	// Arguments
void foo( int a, int b )	// Parameters
```

# Chapter 4
## sizeof
| Type | Size |
|-------|---------|
|bool:           |1 bytes|
|char:           |1 bytes|
|short:          |2 bytes|
|int:            |4 bytes|
|long:           |8 bytes|
|long long:      |8 bytes|
|float:          |4 bytes|
|double:         |8 bytes|
|long double:    |16 bytes|

n-bit signed variable has a range of -(2n-1) to (2n-1)-1.<br>
An n-bit unsigned variable has a range of 0 to (2n)-1.<br>
Overflow leads to undefined behavior.<br>

Fixed width intergers - std::int8_t, std::uint8_t, std::int16_t, std::uint16_t, std::int32_t, std::uint32_t, std::int64_t, std::uint64_t<br>
The size if guaranted on any architecture.They are part of header cstdint<br>

sizeof() return std::size_t type. It is defined in cstddef header file.<br>
If we create an object which is larger than the max size which size_t can hold, then sizeof will not be able to return proper size.<br>

Float has 6-7 digits of precision. Double has 15-16 digits of precision.<br>
Size of float is 4, double is 8. Range of float is less than double
TODO - setprecision and cout of float 

```cpp
int main()
{
    bool a = true;
    std::cout << a << std::endl;    // 1
    std::cout << std::boolalpha;
    std::cout << a << std::endl;    // true
    std::cout << std::noboolalpha;
    std::cout << a << std::endl;    // 1
    std::cout << std::boolalpha;

    bool b{ 0 };
    bool c{ 1 };
    bool d{ 4 };
    std::cout << b << c << d << std::endl;  // falsetruetrue

    bool e;
    // To allow true and false as input.
    // Else 0 and 1 are treated as valid inputs for bool
    // Any incorrect input/numeric value is treated as false
    std::cin >> std::boolalpha;     
    std::cin >> e;
    std::cout << e << std::endl;
}
```

If we input more number of inputs than required for std::cin, then the remaing inputs are buffered in the cin buffer and used for following std::cin inputs.<br>
std::cin.get( ch ); is used to read a single character including whitespaces/newline. std::cin skips whitespaces.<br>


# Chapter 5
const variables cannot be changed ones initialized.<br>

```cpp
void foo( float f )
{
    std::cout << "Foo\n" << f;
}

void foo( double d ) = delete;

int main()
{
    // 1.2 is double and 1.2f is float
    // foo( 1.2 );     // CTE - Use of deleted function. 
    foo( 1.2f );    // OK
}
```

```cpp
// Scientific notation
int main()
{
    double light{ 3.0e8 };
    double lightN{ 3.0e-8 };
    std::cout << light << std::endl;
    std::cout << lightN << std::endl;
}

//3e+08
//3e-08
```

```cpp
#include <bitset>
int main()
{
    int oct{ 023 }, hex{ 0x1B };
    std::cout << oct << std::endl;  // 19
    std::cout << hex << std::endl;  // 27

    int dec{ 28 };
    std::cout << std::hex << dec << std::endl;  // 1c
    std::cout << dec << std::endl;      // Prints in hex, 1c
    std::cout << std::oct << dec << std::endl;  // 34
    std::cout << std::dec << dec << std::endl;  // 28
    std::cout << std::bitset<8>( dec ) << std::endl;    // 00011100
}
```

# const and constexpr
The const keyword is used to declare variables whose values cannot be changed after initialization.<br>
However, it does not guarantee compile-time evaluation. The value of a const variable can be determined at runtime.<br>
The constexpr keyword, introduced in C++11, is used to declare variables or functions that are evaluated at compile-time.<br>

# constexpr functions
```cpp
constexpr int foo( const int x, const int y )
{
    return ( x > y ? x : y );
}

int main()
{
    constexpr int a = 34;
    const int b = 12;
    // int b = 12;  // CTE
    constexpr int ret{ foo( a, b ) };
    std::cout << ret << std::endl;
}
```

```cpp
int main()
{
    constexpr int val{ 23 };

    const int a{ 53 };
    constexpr int val2{ a };

    // CTE
//    int b{ 53 };
//    constexpr int val3{ b };
}
```


# Chapter 6
If either (or both) of the operands are floating point values, the division operator performs floating point division.<br>
Remainder operand only works with integers.<br>
int case of x%y where x, y could be -ve, the output is always of the sign of x.<br>
Prefer the pre-increment/decrement operator as they are faster than the corresponding post operators.<br>

In Case of function call foo( x, ++x ) we will get undefined behavior.<br>
Because C++ does not specify the order in which the args are evaluated.<br>
If first operant is evaluated first foo( 5, 6 ) will be called else foo( 6, 6 ) will be called.<br>
Same with x + ++x;

Comma operator - x,y - Evaluate x, then y and return y.<br>
Comma operator has very low precedance.<br>

# Chapter O
## Bitset
```cpp
#include <bitset>

int main()
{
    std::bitset<8> bit{0b10101010};
    // std::bitset<8> bit{7}; // OK
    std::cout << bit << std::endl;  // 10101010

    bit.set(0);
    std::cout << bit << std::endl;  // 10101011

    bit.flip(0);
    std::cout << bit << std::endl;  // 10101010

    bit.reset(1);
    std::cout << bit << std::endl;  // 10101000

    for( int i = 0; i < 8; ++i )
    {
        if( bit.test(i) )
        {
            std::cout << i << std::endl;    // 3, 5, 7
        }
    }

    bit = bit | std::bitset<8>( 0b11111111 );
    std::cout << bit << std::endl;  // 11111111

    bit = bit & std::bitset<8>( 0 );
    std::cout << bit << std::endl;  // 00000000

    bit = ~bit;
    std::cout << bit << std::endl;  // 11111111

    bit = bit ^ std::bitset<8>( 0b00111100 );
    std::cout << bit << std::endl;  // 11000011

    bit = bit >> 3;
    std::cout << bit << std::endl;  // 00011000

    bit = bit << 3;
    std::cout << bit << std::endl;  // 11000000
}
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

# Chapter 9
## Assert and static_assert
assert condition is checked when program runs. If false, the program terminates.<br>
static_assert condition is checked at compile time. If false, CTE occurs.<br>
assert should not be part of release build. Add statement #define NDEBUG at the top to remove assert from release build.<br>

```cpp
#include <cassert>

// If condition fails CTE occurs
// The condition must be a constant expression.
static_assert( sizeof(int) == 4, "Msg" );

int main()
{
    int y{ 0 };
    
    // If condition fails, program is terminated
    assert( ( 0 != y ) && "msg" );
}
```

`std::cerr` is unbuffered like cout. So it output immediately.<br>
Also it outputs the buffer on differnt error stream.<br>
command > file 2>&1 // Used to redirect both to file
command > file 2> file2 // Used to redirect output to file and error to file2

# Chapter 11 - Function Overload and Templates
```cpp
template< typename T >
bool foo( T x, T y )
{
    return ( x > y ? true : false );
}

int main()
{
    int a = 7, b = 8;
    std::cout << std::boolalpha << foo( a, b ) << std::endl;

    float af = 9.3f, bf = 8.6f;
    std::cout << std::boolalpha << foo( af, bf ) << std::endl;
}

//false
//true
```
Function templates can contain multiple template type parameters and can be overloaded.<br>
```cpp
template< typename T>
void foo( T x, T y )
{
    std::cout << "foo1" << std::endl;
}

template< typename T, typename U >
void foo( T x, U y )
{
    std::cout << "foo2" << std::endl;
}

template< typename T, typename U, typename V >
void foo( T x, U y, V z )
{
    std::cout << "foo3" << std::endl;
}

int main()
{
    foo( 1, 2 );    // foo1
    foo( 1, 2.5 );  // foo2
    foo( 1, 2, 4 ); // foo3
}
```

However, when the compiler is doing template argument deduction, it won’t do any type conversions.<br>
```cpp
template< typename T>
void foo( T x, T y )
{
    std::cout << x << ", " << y << std::endl;
}

int main()
{
    // foo( 1, 2.5 );  // CTE
    foo( static_cast< double >( 1 ), 2.5 );
    foo< int >( 1, 2.5 );
    foo< double >( 1, 2.5 );
}

//1, 2.5
//1, 2
//1, 2.5
```
For non-type template parameters implicit conversions are allowed.<br>\
Allowed conversions - Integral promotions, Integral conversions, User-defined conversions, Lvalue to rvalue conversions.<br> 
```cpp
template< int T >
void foo()
{
    std::cout << "foo " << T << std::endl;
}

int main()
{
    foo<4>();   // foo 4
    // foo();  // CTE
    foo<'c'>(); // foo 99
}
```
```cpp
void foo( int x );
void foo( int x = 9 ) { std::cout << "foo\n"; }

void goo( int x = 9 );
void goo( int x ) { std::cout << "goo\n"; }

void hoo( int x );

int main()
{
    foo();      // OK
    goo();      // OK
    // hoo();   // CTE
}

void hoo( int x = 9 ) { std::cout << "hoo\n"; }
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

# Chapter 17 - 
Passing arrays to a functions and decaying into a pointer.<br>
arr[i] is actually *( arr + i ).<br>
```cpp
// Need to send length of array as seperate parameter
void foo( const int arr[], int length )
{
    std::cout << sizeof( arr ) << std::endl;    // 8
    for( int i= 0; i < length; ++i )
    {
        std::cout << arr[i] << ", ";
    }
    std::cout << std::endl;
}

// Array decays to a pointer
void goo( const int* ptr, int length )
{
    for( int i= 0; i < length; ++i )
    {
        std::cout << *( ptr + i ) << ", ";
    }
    std::cout << std::endl;
}

int main()
{
    int arr[]{ 34, 89, 11, 88, 2 , 9 };
    std::cout << sizeof( arr ) << std::endl;    // 24
    foo( arr, sizeof( arr )/sizeof( arr[0] ) );
    goo( arr, sizeof( arr )/sizeof( arr[0] ) );

    for( int i = 0; i < sizeof( arr )/sizeof( arr[0] ); ++i )
    {
        std::cout << *( arr + i ) << ", ";
    }
    std::cout << std::endl;

    for( auto& i : arr )
    {
        std::cout << i << ", ";
    }
}
```
Multidimentional arrays of cstrings
```cpp
int main()
{
    int alpha{ 'a' };
    char** strings{ nullptr };

    strings = new char*[5];

    for( int i = 0; i < 5; ++i )
    {
        strings[i] = new char[i+2];
        for( int j = 0; j < i+1; ++j )
        {
            strings[i][j] = alpha;
            ++alpha;
        }
        strings[i][i+1] = '\0';
    }

    for( int i = 0; i < 5; ++i )
    {
        std::cout << strings[i] << std::endl;
    }
}

//a
//bc
//def
//ghij
//klmno
```
Print the lenght of the string.<br>
```cpp
int main()
{
    // char arr[5]{ "Hello" }; // CTE
    char arr[50]{ "Hell" };
    std::cout << arr << std::endl;

    // 4, The length of string excluding the null terminator
    std::cout << strlen( arr ) << std::endl;

    // In C++17 and above, Prints 50
    // std::cout << std::size( arr ) << std::endl;
}
```


# Chapter 18 - Iterators
```cpp
// Selection sort
// Smallest num is swapped with the first element
// This continues from next element onwards
int main()
{
    int arr[]{ 20, 60, 40, 10, 22, 45, 70 };
    int len = sizeof( arr )/sizeof( arr[0] );

    for( int i = 0; i < len - 1; ++i )
    {
        int smallest_index = i;
        int smallest_num = arr[i];
        for( int j = i+1; j < len; ++j )
        {
            if( arr[j] < smallest_num )
            {
                smallest_index = j;
                smallest_num = arr[j];
            }
        }

        int temp = arr[i];
        arr[i] = arr[smallest_index];
        arr[smallest_index] = temp;
    }

    for( int i = 0; i < len; ++i )
    {
        std::cout << arr[i] << ", ";
    }
}
```

```cpp
// Sort using algorithm
#include <algorithm>

int main()
{
    int len = 6;
    int arr[]{ 20, 60, 40, 10, 22, 45 };

    std::sort( std::begin( arr ), std::end( arr ) );

    for( int i = 0; i < len; ++i )
    {
        std::cout << arr[i] << ", ";
    }
}
```


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

# Chapter 20
## Function pointers
`int ( *foo )( int, float ) = &goo;`<br>
`int ( *foo )( int, float ) = goo;`<br>
To call the function we can use two ways - <br>
`foo( 23, 45 );        // Implicit dereference`<br>
`( *foo )( 23, 45 );   // Explicit dereference`<br>
`*foo( 23, 45 );       // Wrong. CTE.`<br>
Passing function pointer to other functions -<br>
`void goo( int ( *fptr )( int, int ) )`<br>
`goo( foo );`<br>
Another way is by using the std::function -<br>
`#include <functional>`<br>
`std::function< float( int, float ) > fptr = hoo;`<br>
`fptr( 23, 6 );`<br>
<br>
If function pointer is made NULL, it can lead to undefined behavior.<br>
<br>
Pointer to member function of a class<br>
```cpp
int ( Base::*fptr )( float ) = &Base::foo;
( obj.*fptr )( 23.4 );
( ptr->*fptr )( 13.4 );
```

Array of function pointers - `void( *fptr[] )( int, float ) = { foo, goo, hoo };`<br>
Call --> `( *fptr[i] )( 23, 57.3 );`<br>
Array of function pointers can be used for call back. ( Eg - Event loop )<br>
Call backs can be used to control how a particular part of the function should work. Eg. sort ascending or descending<br>
<br>
Providing default values --> `void foo( int( *fptr )( int, float ) = hoo )`<br>
<br>

## Stack vs Heap
Stack is small in size ( 1-8 MB ). Contains variables and function call stack.<br>
If lot of function calls happen or in case of large data structures, the stack might get filled up causing segmentation fault.<br>
Heap is large in size. Variables allocated dynamically using new, malloc, calloc are assigned on the heap.<br>
Stack memory is allocated/deallocated automatically by compiler. Heap is done by used manually.<br>
Heap is slower and costly.<br>
Stack is contigious. Heap is randomly allocated.<br>

## Command line args
int main( int argc, char** argv )<br>
int main( int argc, char* argv[] )<br>
argc is number of args. Binary name is itself one arg.<br>
argv[i] is the ith arg.<br>

## Ellipsis
```cpp
#include <cstdarg>
void foo( int c, ... )
// A function can take any number of arguments.
va_list v1;
va_start( v1, c ); // First parameter is va_list to initialize. Second is last non-ellipsis argument.
va_arg( v1, int ); // First parameter is va_list. Second parameter is type. Used to take out arg from the list.
va_end( v1 );      // Cleanup the list.
va_copy( v2, v1 ); // No need to start v2. Only end. If we have removed few args from v1 and then done a copy, it would be wrong. Puts random value.
```

## Lambdas
Anyonomous functions that can be defined inside another function<br>
Syntax -<br>
[capture_clause](args)->return value{ commands };<br>

capture_clause allows capturing nearby variables in <br>
[&] - Capture all by reference<br>
[=] - Capture all by value<br>
[&, a] - Capture all by refernce and a by value<br>
[=, &a] - Capture all by refernce and a by reference<br>
[&a, =] - Illegal, default capture should be first<br>
[a, &b] - Capture a by value and b by reference<br>
Variables captured by value are const by default. Add mutable keyword to allow modification<br>
[a]() mutable -> return_value {};<br>
Variables captures by refence are not const except if the original var is const. So no mutable required.<br>
<br>
Lambdas are captures when they are defined.<br>
If variable are captured by value then the values captured at the time of definition are used even if the variable changes value later.<br>
```cpp
int a = 45;
auto lam = [=]()->int{ std::cout << "Lam" << a << std::endl; return a; };	
a = 89;
lam(); // Here output is Lam45
```
In case of capture by reference ,can lead to dangling reference if variable gets<br>
deleted before lambda call.<br>

# Chapter 21 - Operator Overloading
## Overload subscript operator
```cpp
#include <vector>

class Base {
    std::vector< int > vec;
public:
    Base()
    {
        for( int i = 0; i < 10; ++i )
        {
            vec.push_back( i );
        }
    }

    // For non-const objecs
    int& operator[]( const int index )
    {
        return vec[index];
    }

    // For const objects
    const int& operator[]( const int index ) const
    {
        return vec[index];
    }
};

int main()
{
    Base obj;
    obj[5] = 100;	// Allowed

    for( int i = 0; i < 10; ++i )
    {
        std::cout << obj[i] << ", ";
    }
    std::cout << std::endl;

    const Base objc;
    // objc[5] = 100;   // Not allowed

    for( int i = 0; i < 10; ++i )
    {
        std::cout << objc[i] << ", ";
    }
    std::cout << std::endl;
}
```

## Overload parenthesis operator
Functors are classes that operate like functions. Multiplefunctors can be created. Allows class object to be called like a dunction call.<br>
```cpp
class Base {
    int num{};
public:
    int operator()( const int& val )
    {
        num = val;
        return num;
    }

    void operator()()
    {
        std::cout << "No input function\n";
    }

    void print() const
    {
        std::cout << num << std::endl;
    }
};

int main()
{
    Base obj;
    int ret = obj( 23 );
    obj.print();    // 23
    obj();          // No input function
    std::cout << ret << std::endl;  // 23
}
```

## Overload typecast
Used to convert user defined types to other types.<br>
No return type as the return type is same as only one type of return value is allowed.<br>
Implicit conversions are not allowed if explicit keyword ismentioned.<br>
```cpp
class Money {
    int rupee{ 4 };
    int paise{ 50 };
public:
    explicit operator int() const
    {
        return rupee * 100 + paise;
    }
};

void display( int i )
{
    std::cout << i << std::endl;
}


int main()
{
    Money money;
    // display( money ); // This will work if explicit was not mentioned
    display( ( int )money );    // 450
    display( static_cast< int >( money ) ); // 450
}
```

## Overload using normal function
```cpp
class Money {
    int rupee{ 4 };
public:
    int set( int val ) { rupee = val; }
    int get() const { return rupee; }

    bool operator>( const Money& obj ) const
    {
        return rupee > obj.get();
    }
};
```

## Overload using friend function
```cpp
class Money {
    int rupee{ 4 };
public:
    int set( int val ) { rupee = val; }
    int get() const { return rupee; }

    friend bool operator>( const Money& m1, const Money& m2 );
};

bool operator>( const Money& m1, const Money& m2 )
{
    return m1.rupee > m2.rupee;
}
```

## Overload by function outside the class
```cpp
class Money {
    int rupee{ 4 };
public:
    int set( int val ) { rupee = val; }
    int get() const { return rupee; }
};

bool operator>( const Money& m1, const Money& m2 )
{
    return m1.get() > m2.get();
}
```

## Overload I/O operators
Overloading code needs to be written in the left operand. Since we cannot modify ostream, istream we need to<br>
overload outside the class as a normal function.<br>
```cpp
class Money {
    int rupee{ 0};
    int paise{ 0 };
public:
    int get() const { return rupee * 100 + paise; }
    int set( const int valr, const int valp ) { rupee = valr; paise = valp; }

    friend std::ostream& operator<<( std::ostream& os, const Money& m );
    friend std::istream& operator>>( std::istream& is, Money& m );
};

std::ostream& operator<<( std::ostream& os, const Money& m )
{
    os << m.get();
    return os;
}

std::istream& operator>>( std::istream& is, Money& m )
{
    int r, p;
    if( is >> r >> p )  // Check if extraction succedded
    {
        m.rupee = r;
        m.paise = p;
    }
    else
    {
        std::cout << "Extraction fails!!!\n";
    }

    return is;
}

int main()
{
    Money m1;
    std::cout << m1 << std::endl;
    std::cout << "Enter 2 number : " << std::endl;
    std::cin >> m1;
    std::cout << m1 << std::endl;
}

//450
//Enter 2 number : 
//12 50
//1250
```

## Overload unary operator
```cpp
class Base {
public:
    int val{ 34 };

    Base operator-()
    {
        Base ret;
        ret.val = -this->val;
        return ret;
    }

    int get() const
    {
        return val;
    }
};

int main()
{
    Base obj;
    Base obj2 = -obj;
    std::cout << obj2.get() << std::endl;
}
```

## Overload increment/decrement operator
```cpp
class Base {
    int val{};
public:
    Base( const int& v ) : val{ v }
    {
    }

    friend std::ostream& operator<<( std::ostream& os, const Base& b );


    Base& operator++()
    {
        val += 1;
        return *this;
    }

    Base operator++( int )
    {
        Base ret{ *this };
        ++( *this );
        return ret;
    }

    Base& operator--()
    {
        val -= 1;
        return *this;
    }

    Base operator--( int )
    {
        Base ret{ *this };
        --( *this );
        return ret;
    }
};

std::ostream& operator<<( std::ostream& os, const Base& b )
{
    os << b.val;
    return os;
}

int main()
{
    Base obj{ 67 };
    std::cout << obj << std::endl;      // 67
    std::cout << ++obj << std::endl;    // 68
    std::cout << --obj << std::endl;    // 67
    std::cout << obj++ << std::endl;    // 67
    std::cout << obj << std::endl;      // 68
    std::cout << obj-- << std::endl;    // 68
    std::cout << obj << std::endl;      // 67
}
```

## Overloading operator and function template
```cpp
class Money {
    int money{};
public:
    Money( const int& m ) : money{ m }
    {
    }

    int get() const { return money; }

    bool operator>( const Money& m ) const
    {
        return money > m.get();
    }
};

template< typename T >
bool foo( const T& t1, const T& t2 )
{
    return t1 > t2;
}

int main()
{
    Money m1{ 134 };
    Money m2{ 45 };
    std::cout << foo( m1, m2 ) << std::endl;
}
```

# Overload assignment operator
The copy assignment operator (operator=) is used to copy values from one object to another already existing object.<br>
If a new object has to be created before the copying can occur, the copy constructor is used.<br>
If a new object does not have to be created before the copying can occur, the assignment operator is used.<br>
Default copy assignment operator has a problem in case of dynamically allocaed members. The object in which it is copied will point to the same location as original object.<br>
When any one of the object is destroyed, the pointer in other object will be dangling, since destructor of first object destroyes the pointer.<br>
```cpp
class Base {
    int* ptr{ nullptr };
public:
    Base( int val )
    {
        ptr = new int{ val };
    }
    ~Base()
    {
        delete ptr;
    }

    void print() const
    {
        std::cout << *ptr << std::endl;
    }

    int get() const
    {
        return *ptr;
    }

    // Return by reference to enable chaning f1 = f2 = f3
    Base& operator=( const Base& obj )
    {
        // To prevent case of self assignment.
        // In case of self assignment obj2 = obj2, delete ptr will
        // delete the data that needs to be copied.
        if( this == &obj )
            return *this;

        delete ptr;
        ptr = nullptr;

        ptr = new int{ obj.get() };
        return *this;
    }
};

int main()
{
    Base obj{ 45 };
    obj.print();

    {
        Base obj2{ 34 };
        obj2.print();
        obj2 = obj;
        obj2.print();
    }

    obj.print();

    {
        Base obj3{ 34 };
        obj3.print();
        obj3 = obj3;
        obj3.print();
    }
}

//45
//34
//45
//45
//34
//34
```
```cpp
class Base {
    char* str{ nullptr };
    int len{0};

public:
    Base( char* msg, int length ) : len{ length }
    {
        str = new char[len+1];

        for( int i = 0; i < len; ++i )
        {
            str[i] = msg[i];
        }
        str[len] = '\0';
    }

    ~Base()
    {
        delete[] str;
        str = nullptr;
    }

    Base( const Base& b )
    {
        performCopy( b );
    }

    void print() const
    {
        std::cout << str << std::endl;
    }

    int getLength() const
    {
        return len;
    }
    char* getString() const
    {
        return str;
    }

    Base& operator=( const Base& b )
    {
        if( &b == this )
            return *this;

        return performCopy( b );
    }

    Base& performCopy( const Base& b )
    {
        delete[] str;
        str = nullptr;

        len = b.getLength();
        str = new char[len+1];
        char* originalStr = b.getString();
        for( int i = 0; i < len; ++i )
        {
            str[i] = originalStr[i];
        }
        str[len] = '\0';

        return *this;
    }
};

int main()
{
    char* str = "hello world";
    Base obj{ str, strlen( str ) };
    obj.print();

    {
        Base obj2{ obj };
        obj2.print();
    }

    // Destructor will delete the string, which will also delete the string in obj
    // if we use default copy assignment or copy constructor
    obj.print();

    {
        char* str2 = "New Object";
        Base obj3{ str2, strlen( str2 ) };
        obj3.print();
        obj3 = obj;
        obj3.print();
    }

    obj.print();

    std::cout << "End\n";
}

//hello world
//hello world
//hello world
//New Object
//hello world
//hello world
//End
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

Class template specialization
```cpp
template< typename T, int size >
class Base {
    T var;
    int s{ size };
public:
    Base( T x ) : var{ x }
    {
    }

    void print() const;
};

template< typename T, int size >
void Base< T, size >::print() const
{
    std::cout << var << ", " << s << std::endl;
}

template<>
class Base< int, 5 > {
    int var;
public:
    Base( int x ) : var{ x }
    {
    }

    void print() const;
};

void Base< int, 5 >::print() const
{
    std::cout << var << " / " << 5 << std::endl;
}

int main()
{
    Base< int, 5 > base{ 45 };
    base.print();

    Base< int, 10 > base2{ 45 };
    base2.print();

    Base< float, 5 > base3{ 45 };
    base3.print();
}

//45 / 5
//45, 10
//45, 5
```

Partial template class specialization
```cpp
template< typename T, int size >
class Base {
    T var;
    int s{ size };
public:
    Base( T x ) : var{ x }
    {
    }

    void print() const;
};

template< typename T, int size >
void Base< T, size >::print() const
{
    std::cout << var << ", " << s << std::endl;
}

template< int size >
class Base< int, size > {
    int var;
    int s{ size };
public:
    Base( int x ) : var{ x }
    {
    }

    void print() const;
};

template< int size >
void Base< int, size >::print() const
{
    std::cout << var << " / " << s << std::endl;
}

int main()
{
    Base< int, 5 > base{ 45 };
    base.print();

    Base< int, 10 > base2{ 45 };
    base2.print();

    Base< float, 5 > base3{ 45 };
    base3.print();
}

//45 / 5
//45, 10
//45, 5
```

# Chapter 27 - Exceptions
Exceptions of funcdamental type can be caught by value. Non-fundamental type should be caught by const reference.<br>
No type conversion is done for exceptions (so an int exception will not be converted to match a catch block with a double parameter).<br>
When exception occurs, stack unwinding will take place till an appropriate catch block is found.<br>
catch all block `catch(...)` should be placed at the last in sequence. Else CTE.<br>
If a exception is not caught, terminate() is called and destructors are not called.<br>
Exception can be added in operator overloading and also in constructors. All members created will get automatically destroyed. Destructor is not called.<br>
```cpp
void foo()
{
    try
    {
        int e = 2;
        throw e;
    }
    catch(double d)	// Not caught. Stack unwinding continues
    {
        std::cout << "Double\n";
    }
}

void goo()
{
    try
    {
        foo();
    }
    catch( int e )
    {
        std::cout << "Int " << e << "\n";
    }
}

int main()
{
    goo();
}
```

Class type exceptions should be caught using reference to prevent expensive copy.<br>
In case of inheritance class chain, we should specify the derived class catch block first to prevent object slicing.<br>
```cpp
class Base {
};

class Derived : public Base {
};

int main()
{
    try
    {
        Derived obj;    // Derived
        // Base obj;       // Base
        throw obj;
    }
    catch( const Derived& obj )
    {
        std::cout << "Derived\n";
    }
    catch( const Base& obj )
    {
        std::cout << "Base\n";
    }
}
```

`std::exception` class is the base class from which all other class are derived.<br>
If we throw a derived class exception whose copy constructor is deleted, then CTE will occur.<br>
Same exception can be rethrown by using `throw;` in the derived class.<br>
To define a function as non-throwing, we can use the noexcept specifier.<br>
Note that noexcept doesn’t actually prevent the function from throwing exceptions or calling other functions that are potentially throwing.<br>
If an unhandled exception would exit a noexcept function, std::terminate will be called (even if there is an exception handler that would otherwise handle such an exception somewhere up the stack). <br>
```cpp
#include <exception>

class IException : public std::exception
{
    std::string str{};
public:
    IException( const std::string& err ) : str{ err }
    {
    }

    const char* what() const noexcept
    {
        return str.c_str();
    }
};

int main()
{
    try
    {
        throw IException( "Err str" );
    }
    catch( const IException& ex )
    {
        std::cout << ex.what() << std::endl;
    }
}
```
```cpp
class Base {
public:
    virtual void foo() const
    {
        std::cout << "Base\n";
    }
};

class Derived : public Base {
public:
    void foo() const override
    {
        std::cout << "Derived\n";
    }
};

int main()
{
    try
    {
        try
        {
            Derived obj;
            throw obj;
        }
        catch( const Base& o )
        {
            o.foo();
            // throw o;    // This will throw Base
            throw;  // Throws the same which is received
        }
    }
    catch( const Base& o )
    {
        o.foo();    // Derived. Prints Base if throw o is used above
    }
}
```

It might happen that, we allocate memory using new and exception occurs before it is deleted. Flow tranfers to catch bloack skipping the delete.<br>
In such case, allocate and delete the memory outside the try or else use smart pointers.<br>

```cpp
void goo()
{
    try
    {
        throw 1;
    }
    catch(...)
    {
        std::cout << "Catch goo\n";
    }
}

// Program will terminate even if there is a catch in main
// which can handle it
void foo() noexcept
{
    goo();  // Can call functions which are not noexcept
    std::cout << "Foo\n";
    throw 1;
}

int main()
{
    try
    {
        foo();
    }
    catch(...)
    {
        std::cout << "catch\n";
    }
}

//Catch goo
//Foo
//terminate called after throwing an instance of 'int'
//The program has unexpectedly finished.
```

Destructors, implicit constructors, copy & move assignment are considered as non-throwing by default.<br>
Normal functions, user defined constructors, operators are considered as throwing by default.<br>
`noexcept( expression )` will return true or false depending upon the above conditiond.<br>

| Function type | Can resolve exceptions via return statement | Behavior at end of catch block |
|----------------|-----------------|--------------|
| Constructor | No, must throw or rethrow | Implicit rethrow |
| Destructor | Yes | Implicit rethrow |
| Non-value returning function | Yes | Resolve exception |
| Value returning function | Yes | Undefined behavior |

```cpp
class Base {
public:
    Base()
    {
        std::cout << "Base\n";
        // throw 1;
    }
};

class Derived : public Base {
public:
    Derived() try : Base{}
    {
        std::cout << "Derived\n";
    }
    catch(...)
    {
        std::cout << "Derived catch\n";
        throw;
    }

    int foo() const try
    {
        std::cout << "Foo\n";
        throw 1;
    }
    catch(...)
    {
        std::cout << "Derived foo catch\n";
        return 8;
    }
};

int main()
{
    try
    {
        Derived obj;
        std::cout << obj.foo() << "\n";
        std::cout << "Main\n";
    }
    catch(...)
    {
        std::cout << "catch\n";
    }
}

//Base
//Derived
//Foo
//Derived foo catch
//8
//Main
```
