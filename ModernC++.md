# Vector
```cpp
void print( const std::vector< int >& vec )
{
    std::vector< int >::const_iterator it;
    for( it = vec.begin(); it != vec.end(); ++it )
    {
        std::cout << *it << ", ";
    }
    std::cout << std::endl;
}

int main()
{
    std::vector< int > vec;
    vec.push_back( 5 );
    vec.push_back( 15 );
    vec.push_back( 53 );

    print( vec );   // 5, 15, 53

    std::cout << vec.front() << std::endl;  // 5
    std::cout << vec.back() << std::endl;   // 53
    vec.pop_back();

    for( int i = 0; i < vec.size(); ++i )
    {
        std::cout << vec[i] << ", ";
    }   // 5, 15,
    std::cout << std::endl;

    std::cout << *( vec.data() ) << std::endl;  // 5 .Returns pointer to underlying vector

    vec.clear();    // Remove all elements from vector

    if( vec.empty() )   // Check if vector is empty
        std::cout << "Empty\n";     // Empty

    std::cout << "Size = " << vec.size() << std::endl;  // 0
    std::cout << "Capacity = " <<  vec.capacity() << std::endl; // 4
}
```
