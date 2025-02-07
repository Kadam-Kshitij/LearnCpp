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
# strncpy
Copies the first num characters of source to destination. If the end of the source C string (which is signaled by a null-character) is found before num characters have been copied, destination is padded with zeros until a total of num characters have been written to it.<br>
No null-character is implicitly appended at the end of destination if source is longer than num. Thus, in this case, destination shall not be considered a null terminated C string (reading it as such would overflow).<br>
No overlapping.<br>
```cpp
#include <stdio.h>
#include <string.h>

int main ()
{
  char str1[]= "To be or not to be";
  char str2[40];
  char str3[40];

  /* copy to sized buffer (overflow safe): */
  strncpy ( str2, str1, sizeof(str2) );

  /* partial copy (only 5 chars): */
  strncpy ( str3, str2, 5 );
  str3[5] = '\0';   /* null character manually added */

  puts (str1);
  puts (str2);
  puts (str3);

  return 0;
}
```
# strcpy
Copies the C string pointed by source into the array pointed by destination, including the terminating null character (and stopping at that point).<br>
To avoid overflows, the size of the array pointed by destination shall be long enough to contain the same C string as source (including the terminating null character), and should not overlap in memory with source.<br>
```cpp
int main()
{
    char str[] = "Hello Hello Hello Hello Hello Hello Hello Hello Hello";
    std::cout << str << ", " << sizeof( str ) << ", " << strlen( str ) << std::endl;
    // Hello Hello Hello Hello Hello Hello Hello Hello Hello, 54, 53

    char str1[20] = "Alex Well";
    std::cout << str1 << ", " << sizeof( str1 ) << ", " << strlen( str1 ) << std::endl;
    // Alex Well, 20, 9

    memcpy( str1, str, ( sizeof( str1 ) - 1 ) * sizeof( char ) );
    str1[sizeof( str1 ) - 1] = '\0';

    std::cout << str1 << ", " << sizeof( str1 ) << ", " << strlen( str1 ) << std::endl;
    // Hello Hello Hello H, 20, 19

    char str3[100];
    strcpy( str3, str );
    std::cout << str3 << ", " << sizeof( str3 ) << ", " << strlen( str3 ) << std::endl;
    // Hello Hello Hello Hello Hello Hello Hello Hello Hello, 100, 53
}
```
# Find character in string
strrchr - Get the last occurance of character in a string.<br>
strstr - ( str1, str2 ) - Returns pointer to first occurance of str2 in str1. Else returns NULL pointer.<br>
```cpp
int main()
{
    char str[] = "Hello Pello Qello Hello Hello Hello Hello Hello Hello";
    // Returns pointer to first occurance of character in a string.
    // Returns Null if not present.
    char* ch = strchr( str, 'Q' );

    if( !ch )
        std::cout << "Not found\n";
    else
        std::cout << ch << std::endl;   // Qello Hello Hello Hello Hello Hello Hello

    ch = strchr( str, 'Z' );
    if( !ch )
        std::cout << "Not found\n"; // Not found
    else
        std::cout << ch << std::endl;
}
```
# Other functions 
strcat( dest, src ) - Replace terminating char of dest with starting char of src. Adding terminating char to the new string formed. Check size to prevent overflow.<br>
strncat( dest, src, num ) - Appends the first num characters of source to destination, plus a terminating null-character.<br>
strncmp( str1, str2, num ) - Return 0 if equal. Return < 0 if first mismatch char in str1 < str2. Else > 0.<br>

# Split string
```cpp
int main()
{
    char str[] = "This is a string, Hello!";
    char tk[] = " , !.";
    char* token;

    token = strtok( str, tk );
    while( NULL != token )
    {
        std::cout << token << std::endl;
        token = strtok( NULL, tk );
    }

    return 0;
}

//This
//is
//a
//string
//Hello
```
