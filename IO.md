# cin
```cpp
int main()
{
    std::cout << "Enter a line\n";
    std::string str;
    int i;
    std::cin >> str >> i;
    std::cout << str << ", " << i << std::endl;

    std::cin.ignore( std::numeric_limits< std::streamsize >::max(), '\n' );
    // Empty the cin buffer.
    // If not done below getline will read the \n remaining in the buffer

    std::string line;
    std::getline( std::cin, line ); // Waits for user input if cin buffer is empty
    std::cout << line << std::endl;

    // Validating inputs
label:
    std::cout << "Enter a number : ";
    std::cin >> i;
    if( std::cin.fail() )
    {
        std::cout << "Incorrect input\n";
        std::cin.clear();
        std::cin.ignore(std::numeric_limits< std::streamsize >::max(), '\n');
        goto label;
    }
    else
        std::cout << i << std::endl;

    std::cin.ignore(std::numeric_limits< std::streamsize >::max(), '\n');

    char buff[10]; // Number of chars to read.
    std::cin.read( buff, 10 );  // Waits till 10 chars are available in the cin buffer.
    std::cout << buff << std::endl;

    std::cin.ignore(std::numeric_limits< std::streamsize >::max(), '\n');

    std::cout << "End" << std::endl;
}
```
# file read
```cpp
int main()
{
    std::fstream file( "k.txt", std::ios::in );
    if( file.is_open() )
    {
//        std::string str;
//        while( file >> str )
//            std::cout << str;   // Abc33Mno99Xyz82Pqr31

//        char ch;
//        while( file >> ch )
//            std::cout << ch;    // Abc33Mno99Xyz82Pqr31   // Skip spaces and new line

//        char ch;
//        while( file.get( ch ) )
//            std::cout << ch;  // Does not skip spaces and new line
////        Abc 33
////        Mno 99
////        Xyz 82
////        Pqr 31

//        std::string line;
//        while( std::getline( file, line ) )
//        {
//            std::cout << line;  // Abc 33Mno 99Xyz 82Pqr 31
//        }

//        char buff[50];
//        while( file.getline( buff, sizeof( buff ) ) )
//            std::cout << buff;  // Abc 33Mno 99Xyz 82Pqr 31

        file.close();
    }
    else
    {
        std::cout << "File not open\n";
    }

    return 0;
}
```
