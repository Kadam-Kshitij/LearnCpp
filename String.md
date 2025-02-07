# Creating C-Strings
```cpp
int main()
{
    char str[] = "Hello World!";
    char str1[] = { 'H', 'e', 'l', 'l', ' ', 'W', '\0' };
    char* str2 = "Abc Def";
    const char* str3 = "Xyz MNO";

    std::cout << str << std::endl;
    std::cout << str1 << std::endl;
    std::cout << str2 << std::endl;
    std::cout << str3 << std::endl;

    // str2[2] = 'P';  // Run time error
    // str3[2] = 'P';  // Compile time error
}
```
