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

|  | public	|	protected |	private |			
| ( Type of inhe. ) | | | |
|-------------------|-------------|---------------|
|public |            public |      protected |  X |
|protected |         protected |   protected |  X |
|private |           private |     private |    X |
