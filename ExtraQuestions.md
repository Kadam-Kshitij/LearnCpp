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
