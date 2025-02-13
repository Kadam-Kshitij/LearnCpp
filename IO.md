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
