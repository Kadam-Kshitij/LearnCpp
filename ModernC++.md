# Vector
Dynamic array that can resize itself when elements are added or removed.<br>
Stores elements in a contiguous memory location.<br>
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

    std::vector< int > vec2{ 4, 5, 6 };
    std::vector< int > vec3{ 11, 12, 16 };
    vec3.swap( vec2 );
    print( vec2 );  // 11, 12, 16,
    print( vec3 );  // 4, 5, 6,
}
```
```cpp
class Base {
    int val{};
public:
    Base( int x, int y ) : val{ x + y } {}
    friend std::ostream& operator<<( std::ostream& os, const Base& obj );
};

std::ostream& operator<<( std::ostream& os, const Base& obj )
{
    os << obj.val;
    return os;
}

void print( const std::vector< Base >& vec )
{
    std::vector< Base >::const_iterator it;
    for( it = vec.begin(); it != vec.end(); ++it )
    {
        std::cout << *it << ", ";
    }
    std::cout << std::endl;
}

int main()
{
    std::vector< Base > vec;
    vec.push_back( Base( 15, 5 ) );
    vec.push_back( Base( 96, 4 ) );
    vec.push_back( Base( 25, 5 ) );
    vec.push_back( Base( 35, 5 ) );
    vec.push_back( Base( 57, 3 ) );

    print( vec );   // 55, 56, 25, 35, 57,

    vec.emplace( vec.begin() + 2, 8, 7 );   // Construct element in place. 8 and 7 are constructor inputs
    print( vec );   // 20, 100, 15, 30, 40, 60,
    // Already constructed element is not required as in insert

    vec.erase( vec.begin() + 1, vec.begin() + 3 );  // Erase elements from [ first , last )
    print( vec );   // 20, 30, 40, 60,

    Base obj( 100, 200 );
    vec.insert( vec.begin() + 1, obj ); // Insert element which is already constructed
    print( vec );   // 20, 300, 30, 40, 60,

    std::vector< Base > vec2 = vec;
    vec.insert( vec.begin() + 2, vec2.begin(), vec2.end() );
    print( vec );   // 20, 300, 20, 300, 30, 40, 60, 30, 40, 60,
}
```
```cpp
int main()
{
    std::vector< int > vec;
    std::cout << "Size = " << vec.size() << std::endl;  // 0
    std::cout << "Capacity = " << vec.capacity() << std::endl;  // 0

    vec.push_back( 1 );
    std::cout << "Size = " << vec.size() << std::endl;  // 1
    std::cout << "Capacity = " << vec.capacity() << std::endl;  // 1

    vec.push_back( 2 );
    std::cout << "Size = " << vec.size() << std::endl;  // 2
    std::cout << "Capacity = " << vec.capacity() << std::endl;  // 2

    vec.push_back( 2 );
    std::cout << "Size = " << vec.size() << std::endl;  // 3
    std::cout << "Capacity = " << vec.capacity() << std::endl;  // 4

    vec.push_back( 2 );
    vec.push_back( 2 );
    std::cout << "Size = " << vec.size() << std::endl;  // 5
    std::cout << "Capacity = " << vec.capacity() << std::endl;  // 8
}
```
```cpp
int main()
{
    std::vector< int > vec;
    std::cout << "Size = " << vec.size() << std::endl;  // 0
    std::cout << "Capacity = " << vec.capacity() << std::endl;  // 0

    vec.push_back( 1 );
    vec.push_back( 1 );
    vec.push_back( 1 );
    vec.push_back( 1 );
    vec.push_back( 1 );
    vec.push_back( 1 );
    std::cout << "Size = " << vec.size() << std::endl;  // 6
    std::cout << "Capacity = " << vec.capacity() << std::endl;  // 8
}
```
# Array
```cpp
int main()
{
    std::array< int, 5 > arr{ 3, 6, 1, 6, 8 };
    for( int i = 0; i < arr.size(); ++i )
    {
        std::cout << arr[i] << ", ";
    }   // 3, 6, 1, 6, 8,

    // Same functions as vector
    // empty, at, front, back, data, swap 
}
```
