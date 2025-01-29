## printf
```cpp
int main()
{
    int d = 23;
    float f = 22;
    char* str = "World";

    // Hello 23 17 27 22.000000 World W
    printf( "Hello %d %x %o %f %s %c\n", d, d, d, f, str, str[0] );

    int* ptr = &d;
    printf( "%p %d", ptr, *ptr );   // 0x7ffd2d9de930 23
}
```
