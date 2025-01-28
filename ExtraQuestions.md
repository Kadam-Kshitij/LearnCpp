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
