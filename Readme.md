# Chapter 14 - Introduction to Classes

In C struct cannot have member functions. <br>
In C++ struct can contain both member functions and member variables.


Const Objects --->
Ones created, we cannot modify the member variables of const object.
We cannot call member functions which can modify the member variables.

Const objects can call only const member functions.
Non-const objects can call both const and non-const member functions.

If a member function does not modify the member variables but is not defined as const,
then const objects cannot access the function.


Access Specifiers --->
The members of struct are public by default.
The members of class are private by default.

Struct inherit publicly while class inherit privately.

Access specifier
Public : Can be accessed from anywhere
Private : Can be accessed within the class and in friend functions/class
Protected : Within the class, friend functions/class and in derived classes.

Data members can be returned by const lvalue reference to prevent expensive copying.
const int& foo(){ return m_x; } // m_x is a data member
The lifetime of a data member will be equal to lifetime of the object.

Const member functions cannot return non-const reference to data members.

Data Hiding - Make data members private


If class has public member functions,
class Base{
public:
	int x, y;
};
Then we can use aggregate initialization Base obj{ 34, 56 }; to initialize x and y.
This will not work if x, y were private. Making data member private makes the class a non-aggregate.

During constructor call, implicit conversions can happen.
Base( int a, int b )	// Constructor
Base obj( true, 'a' );
Here implicit conversion will happen


Member initializer list
Derived( const int& x, const int& y ) : Base{ x }, m_y{ y }
Uses -
1) Call base constructors
2) Initialize reference and const member variables.
3) Increases speed - Variables are initialized directly rather than default
initialization and then assigning values in constructor body.
Order of initialization happens in the order in which they are defined in the class,
and not in the order in which they are defined in the member initialization list.
class Base
{
    int y;
    int x;
public:
    Base( const int& valx, const int& valy ) : x{ valx }, y{ x }
    {
    }
};
In this case y will be assigned first a garbage value of x,
and then x will be assigned valx value.

Constructor
Default constructor - Has no parameters.
Constructors can be overloaded.
If no default constructor is provided by user, the compiler providees one.
If any type of constructor is specified by user, default constructor is not provided by compiler.
We can tell the compiler to provide a default constructor using
Base() = default; // generates an explicitly defaulted default constructor
Constructors can have default values
Base( int x, int y = 0 )
Delegating constructors - One constructor can call another constructor via the initializer list
Base( int x ) : Base{ x, 0 }
A constructor which delegates( calls another constructor ) is not allowed to do any initialization.
One constructor may call another constructor and that constructor can again call the first. Leading to a infinite loop and stack may get filled up.

Copy constructor
Base( const Base& obj )
const is not mandatory, but should be used to avoid accidentally modifying the paramter.
& is mandatory. If not used will cause CTE.
Compiler provides a copy constructor if not provided by user.
When constructor is passed by value to a function, the arg is copied into the parameter using copy constructor.
We can request for a compiler provided copy constructor using
Base( const Base& obj ) = default;
If we dont want to create copies, we can delete the copy constructor.
Base( const Base& obj ) = delete;

Use cases -
1) Function call with pass by value
2) Function return with pass by value 		****
3) Creating new object from already existing object.

Explicit keyword
Prevent implicit conversions from different types to class type
Suppose a function takes class object by value as parameter, but we send int as argument during function call,
then the int will get converted to class object.
Similarly a function is returning class object by value, but the function is actually returing int,
then implicit conversion from int to class will happen.
If explicit keyword is used, then such implicit conversions are not allowed.
