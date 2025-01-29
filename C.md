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

# Functions
`int system (const char* command);`<br>
`void perror( const char* str )`    // Prints the str followed by error depending on the errno set.<br>
`errno` is a global variable which is set by various libs when failure occurs. Reset value is 0.<br>
`ssize_t read(int fd, void *buf, size_t count);` Read max count number of bytes from fd and store in buf. Returns bytes read.<br>
`ssize_t write(int fd, const void *buf, size_t count);` Write count number of bytes from buf to fd. Returns bytes written.<br>
`int close(int fd);` used to close a file descriptor.<br>
`int open(const char *pathname, int flags, mode_t mode);`


# Memory operations
`void * memcpy ( void * destination, const void * source, size_t num );` - Used in case of non-overlapping copy.<br>
`void * memmove ( void * destination, const void * source, size_t num );` - Used in case of Overlapping copy. Bytes are first copied to an intermediate location and then to final position. Slower than memcpy.<br>
`void * memset ( void * ptr, int value, size_t num );` - Used to set value for series of bytes.<br>
```cpp
int main()
{
    int* ptr = ( int* )malloc( 10 * sizeof( int ) );
    for( int i = 0; i < 10; ++i )
    {
        ptr[i] = i + 1;
        printf( "%d, ", ptr[i] );   // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10,
    }
    printf( "\n" );

    int* cptr = ( int* )calloc( 10, sizeof( int ) );
    for( int i = 0; i < 10; ++i )
    {
        printf( "%d, ", cptr[i] );  // 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
    }
    printf( "\n" );

    memcpy( cptr + 4, ptr, 6 * sizeof( int ) );
    for( int i = 0; i < 10; ++i )
    {
        printf( "%d, ", cptr[i] );  // 0, 0, 0, 0, 1, 2, 3, 4, 5, 6,
    }
    printf( "\n" );

    // memcpy does not give expected output for overlapping copy like below
//    memcpy( ptr + 4, ptr, 6 * sizeof( int ) );
//    for( int i = 0; i < 10; ++i )
//    {
//        printf( "%d, ", ptr[i] );  // 1, 2, 3, 4, 1, 2, 3, 4, 1, 2,
//    }
//    printf( "\n" );

    memmove( ptr + 4, ptr, 6 * sizeof( int ) );
    for( int i = 0; i < 10; ++i )
    {
        printf( "%d, ", ptr[i] );  // 1, 2, 3, 4, 1, 2, 3, 4, 5, 6
    }
    printf( "\n" );

    memset( ptr + 5, -1, 5 * sizeof( int ) );
    for( int i = 0; i < 10; ++i )
    {
        printf( "%d, ", ptr[i] );  // 1, 2, 3, 4, 1, -1, -1, -1, -1, -1,
    }
    printf( "\n" );
}
```
