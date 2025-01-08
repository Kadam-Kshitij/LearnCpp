# Chapter 14 - Introduction to Classes

In C struct cannot have member functions. <br>
In C++ struct can contain both member functions and member variables.<br>
<br>
## Const Objects
`const Base obj;`<br>
Ones created, we cannot modify the member variables of const object. Member variables can be initialized in constructor body/ initializer list<br>
const object cannot call member functions which can modify the member variables.<br>
const objects can call only const member functions `void foo() const )`<br>
Non-const objects can call both const and non-const member functions.<br>
If a member function does not modify the member variables but is not defined as const, then const objects cannot access the function.<br>
<br>
It is possible to overload two functions with same name and signature but dirrerent constness.<br>
Const object will call the const function and non const objects will call the non const function.<br>
<br>
## Access Specifiers
The members of struct are public by default.<br>
The members of class are private by default.<br>
<br>
Struct inherit publicly while class inherit privately.<br>
<br>
Access specifier<br>
Public : Can be accessed from anywhere<br>
Private : Can be accessed within the class and in friend functions/class<br>
Protected : Within the class, friend functions/class and in derived classes<br>
<br>
Data members can be returned by const lvalue reference to prevent expensive copying.<br>
const int& foo(){ return m_x; } // m_x is a data member<br>
The lifetime of a data member will be equal to lifetime of the object.<br>
<br>
Const member functions cannot return non-const reference to data members.<br>
<br>
Data Hiding - Make data members private<br>
<br>
If class has public member functions,<br>
class Base{<br>
public:<br>
	int x, y;<br>
};<br>
Then we can use aggregate initialization Base obj{ 34, 56 }; to initialize x and y.<br>
This will not work if x, y were private. Making data member private makes the class a non-aggregate.<br>
<br>
During constructor call, implicit conversions can happen.<br>
Base( int a, int b )	// Constructor<br>
Base obj( true, 'a' );<br>
Here implicit conversion will happen<br>
<br>
## Member initializer list
Derived( const int& x, const int& y ) : Base{ x }, m_y{ y }<br>
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
Default constructor - Has no parameters. If no default constructor is provided by user, the compiler providees one.<br>
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
Base( int x ) : Base{ x, 0 }<
{
}
Base( int x, int y ) : m_x{ x }, m_y{ y }
{
}
```

A constructor which delegates( calls another constructor ) is not allowed to do any initialization.<br>
One constructor may call another constructor and that constructor can again call the first. Leading to a infinite loop and stack may get filled up.<br>

Constructors can be overloaded.<br>

## Copy constructor
Base( const Base& obj )<br>
const is not mandatory, but should be used to avoid accidentally modifying the paramter.<br>
& is mandatory. If not used will cause CTE.<br>
Compiler provides a copy constructor if not provided by user.<br>
When constructor is passed by value to a function, the arg is copied into the parameter using copy constructor.<br>
We can request for a compiler provided copy constructor using<br>
Base( const Base& obj ) = default;<br>
If we dont want to create copies, we can delete the copy constructor.<br>
Base( const Base& obj ) = delete;<br>
<br>
Use cases -<br>
1) Function call with pass by value<br>
2) Function return with pass by value 		****<br>
3) Creating new object from already existing object.<br>
<br>
## Explicit keyword
Prevent implicit conversions from different types to class type<br>
Suppose a function takes class object by value as parameter, but we send int as argument during function call,<br>
then the int will get converted to class object.<br>
Similarly a function is returning class object by value, but the function is actually returing int,<br>
then implicit conversion from int to class will happen.<br>
If explicit keyword is used, then such implicit conversions are not allowed.<br>
