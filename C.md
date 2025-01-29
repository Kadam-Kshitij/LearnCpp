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
## Memory allocation
Malloc aloocates memory but does not initialize it. Calloc allocates and initializes memory to zero. Hence calloc is slower.<br>
Realloc is used to resize memory allocated by malloc/calloc. Resized memory can be at a different location.<br>
If new memory is less than initial memory, then initial values are retained. If new memory is more than initial memory, additional locations are un initialized. Previous values are retained.<br>
free on a null ptr does nothing. Double free() can lead to undefined behavior.<br>
```cpp
int main()
{
    int* mptr = ( int* )( malloc( 5 * sizeof( int ) ) );
    if( NULL == mptr )
    {
        printf( "Malloc failed" );
        exit(EXIT_FAILURE);
    }
    for( int i = 0; i < 5; ++i )
    {
        mptr[i] = i + 1;
    }
    for( int i = 0; i < 5; ++i )
    {
        printf( "%d ", mptr[i] );
    }
    printf( "\n" );

    int* cptr = ( int* )( calloc( 5 , sizeof( int ) ) );
    if( NULL == cptr )
    {
        printf( "Malloc failed" );
        exit(EXIT_FAILURE);
    }
    for( int i = 0; i < 5; ++i )
    {
        printf( "%d ", cptr[i] );
    }
    free( cptr );
    printf( "\n" );

    int* rptr = ( int* ) realloc( mptr, 10 * sizeof( int ) );
    if( NULL == rptr )
    {
        free( mptr );
        printf( "Realloc failed\n" );
        exit(EXIT_FAILURE);
    }
    for( int i = 0; i < 10; ++i )
    {
        printf( "%d ", rptr[i] );
    }

    free( rptr );
}
/*
1 2 3 4 5
0 0 0 0 0
1 2 3 4 5 0 0 0 0 0*/
```

## String to numeric conversions
atoi, atof, atol, atoll `int atoi( const char* str );`<br>
strtof, strtod, strtold `float strtof( const char* str, char** endptr )`<br>
strtol, strtoll, strtoul, strtoull `long int strtol (const char* str, char** endptr, int base);`<br>
```cpp
int main()
{
    // 1) Discards whitespaces
    // 2) Takes optional +/- sign
    // 3) Considers numbers till non-numeric characters
    // 4) Ignores rest of the chars
    char* str = "  -1234s45";
    int val = atoi( str );
    printf( "%d", val );    // -1234

    // If first non white space char is not a valid numeric, then returns zero
    char* str2 = "  d-1234s45";
    int val2 = atoi( str2 );
    printf( "%d", val2 );    // 0
}
```

`int system (const char* command);`<br>
