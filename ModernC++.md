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
When the size goes above capacity, the capacity is doubled.<br>
Vector is totally reallocated when capacity is exceded.<br>
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
The size of std::array is fixed at compile-time and cannot be modified after initialization.<br>
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
# Queue
Implements FIFO
```cpp
void print( const std::queue< int >& queue )
{
    std::queue< int > copy{ queue };
    while( !copy.empty() )
    {
        std::cout << copy.front() << ", ";
        copy.pop();
    }
    std::cout << std::endl;
}


int main()
{
    std::queue< int > qu;
    qu.push( 55 );
    qu.push( 89 );
    qu.push( 45 );
    print( qu );    // 55, 89, 45,
    qu.pop();
    print( qu );    // 89, 45,
    qu.emplace( 877 );  // Creates element in place at the end of queue
    print( qu );    // 89, 45, 877,

    // front, back, swap, empty, size
}
```
# Stack
Implements LIFO/FILO
```cpp
void print( const std::stack< int >& st )
{
    std::stack< int > copy{ st };
    while( !copy.empty() )
    {
        std::cout << copy.top() << ", ";
        copy.pop();
    }
    std::cout << std::endl;
}

int main()
{
    std::stack< int > st;
    st.push( 55 );
    st.push( 89 );
    st.push( 45 );
    print( st );    // 45, 89, 55,
    st.pop();
    print( st );    // 89, 55,
    st.emplace( 877 );  // Creates element in place at the top of stack
    print( st );    // 877, 89, 55,

    // top, swap, empty, size
}
```
```cpp
class Base {
    int val{};
public:
    Base( const int& x ) : val{ x } {}
    friend std::ostream& operator<<( std::ostream& os, const Base& obj );
    bool operator>( const Base& obj ) const
    {
        return val > obj.get();
    }
    int get() const { return val; }
};

std::ostream& operator<<( std::ostream& os, const Base& obj )
{
    os << obj.val;
    return os;
}

void print( std::priority_queue< Base, std::vector< Base >, std::greater< Base > > pqu )
{
    std::priority_queue< Base, std::vector< Base >, std::greater< Base > > copy{ pqu };
    while( !copy.empty() )
    {
        std::cout << copy.top() << ", ";
        copy.pop();
    }
    std::cout << std::endl;
}

int main()
{
    // Type, Underlying container to store the elemens, Compare operator
    std::priority_queue< Base, std::vector< Base >, std::greater< Base > > pqu;
    pqu.emplace(  Base( 109 ) );
    pqu.push( Base( 59 ) );
    pqu.push( Base( 69 ) );
    pqu.push( Base( 39 ) );
    pqu.push( Base( 49 ) );
    pqu.push( Base( 68 ) );
    // Order is reverse due to priority
    print( pqu );    // 39, 49, 59, 68, 69, 109,
    emplace( 50 );
    print( pqu );
    pqu.emplace( 50 );
    print( pqu );   // 39, 49, 50, 59, 68, 69, 109,

    std::cout << std::endl;
    // top, swap, empty, size
}
```
# Deque
Fast insertions/deletion at both ends of the queue.<br>
Not stored contiguously. Typical implementations use a sequence of individually allocated fixed-size arrays, with additional bookkeeping.<br>
The storage of a deque is automatically expanded and contracted as needed.<br>
Expansion of a deque is cheaper than the expansion of a std::vector because it does not involve copying of the existing elements to a new memory location.<br>
`push_front, push_back, pop_back, pop_front`
`at, [], front, back, size, empty, clear, insert, emplace, erase, swap`

# Set
```cpp
class Base {
    int val{};
public:
    Base( const int& x ) : val{ x } {}
    friend std::ostream& operator<<( std::ostream& os, const Base& obj );
    bool operator>( const Base& obj ) const
    {
        return val > obj.get();
    }
    int get() const { return val; }
};

std::ostream& operator<<( std::ostream& os, const Base& obj )
{
    os << obj.val;
    return os;
}

int main()
{
    std::set< Base, std::greater< Base > > set;
    set.insert( Base( 109 ) );
    set.insert( Base( 59 ) );
    set.insert( Base( 69 ) );
    set.insert( Base( 39 ) );
    set.insert( Base( 49 ) );
    set.insert( Base( 68 ) );

    std::set< Base >::iterator it;
    for( it = set.begin(); it != set.end(); ++it )
    {
        std::cout << *it << ", ";
    }   // 109, 69, 68, 59, 49, 39,
    std::cout << std::endl;
}
```
# Map
```cpp
#include <map>

void print( std::map< int, std::string, std::greater< int > > map )
{
    for( std::pair< int, std::string > i : map )
    {
        std::cout << i.first << "," << i.second << " ; ";
    }
    std::cout << std::endl;
}

int main()
{
    std::map< int, std::string, std::greater< int > > map;
    map[1] = "A";
    map[26] = "Z";
    map[11] = "K";
    print( map ); // 26,Z ; 11,K ; 1,A ;

    map.emplace( std::make_pair< int, std::string >( 4, "D" ) );
    print( map ); // 26,Z ; 11,K ; 4,D ; 1,A ;

    map.erase( 11 );
    print( map );   // 26,Z ; 4,D ; 1,A ;

    std::map< int, std::string, std::greater< int > >::iterator it = map.find( 26 );
    std::cout << it->first << "," << it->second << std::endl;   // 26,Z

    map.insert( std::make_pair< int, std::string >( 5, "E" ) );
    print( map );   //26,Z ; 5,E ; 4,D ; 1,A ; 
}
```
# Iterators
```cpp
int main()
{
    std::vector< int > vec{ 71, 77, 1, 58, 22, 79, 90 };
    std::vector< int >::iterator it;

    for( it = vec.begin(); it != vec.end(); ++it )
    {
        // *it = 8; // Ok - Allowed
        std::cout << *it << ", ";
    }   // 71, 77, 1, 58, 22, 79, 90,
    std::cout << std::endl;

    std::vector< int >::reverse_iterator itr;
    for( itr = vec.rbegin(); itr != vec.rend(); ++itr )
    {
        // *itr = 8; // Ok - Allowed
        std::cout << *itr << ", ";
    }   // 90, 79, 22, 58, 1, 77, 71,
    std::cout << std::endl;

    std::vector< int >::const_iterator itc;
    for( itc = vec.cbegin(); itc != vec.cend(); ++itc )
    {
        // *itc = 12; // Not allowed
        std::cout << *itc << ", ";
    }   // 71, 77, 1, 58, 22, 79, 90,
    std::cout << std::endl;

    std::vector< int >::const_reverse_iterator itcr;
    for( itcr = vec.crbegin(); itcr != vec.crend(); ++itcr )
    {
        // *itcr = 12; // Not allowed
        std::cout << *itcr << ", ";
    }   // 90, 79, 22, 58, 1, 77, 71,
    std::cout << std::endl;
}
```


| Type | Push/Pop | Iterator | Funtions | Modification | Use |
|------|----------|----------|----------|-----|----------|
| std::array< int , 6 > | No | Yes | at, data, front, back, [], size, empty, swap |  | When size if know at compile time and is fixed |
| std::vector< int > | push_back, pop | Yes | at, data, front, back, [], size, empty, swap, capacity | clear, insert, emplace, erase | The elements are stored contiguously. The elements are stored contiguously.  Vectors usually occupy more space than static arrays, because more memory is allocated to handle future growth. This way a vector does not need to reallocate each time an element is inserted, but only when the additional memory is exhausted. |  
| std::deque< int > | push_back, push_front, pop_back, pop_front | Yes | at, [], size, empty, back, front, swap | emplace, emplace_back, emplace_front, insert, erase, clear | Fast insertions/deletion at both ends of the queue. Not stored contiguously. Typical implementations use a sequence of individually allocated fixed-size arrays, with additional bookkeeping. The storage of a deque is automatically expanded and contracted as needed. Expansion of a deque is cheaper than the expansion of a std::vector because it does not involve copying of the existing elements to a new memory location. |
| std::list | push_back, push_front, pop_back, pop_front | Yes | front, back, size, empty, swap | emplace, emplace_back, emplace_front, insert, erase, clear |std::list is a container that supports constant time insertion and removal of elements from anywhere in the container. Fast random access is not supported. It is usually implemented as a doubly-linked list. |
| std::queue< int > | push, pop | No | front, back, size, swap, empty | emplace( back ) | Implements FIFO. Underlying implementation is std::deque |
| std::stack< int > | push, pop | No | top, empty, size, swap | emplace( top ) | Implements LIFO. Underlying implementation is std::deque |
| std::priority_queue< int, std::vector< int >, std::greater< int > > | push, pop | No | top, empty, size, swap | emplace | Underlying implementation is std::vector. Order is reverse due to priority. |
| std::set< int, std::greater< int > > | No | Yes | empty, size, swap, count, find | clear, insert, emplase, erase | Contains a sorted set of unique objects. Search, removal, and insertion operations have logarithmic complexity. Implemented as Red-Black Tree |
| std::map< int, std::string, std::greater< int > > | No | Yes | at, [], empty, size, swap, count, find | clear, insert, emplace, erase |  Contains key-value pairs with unique keys. Keys are sorted. Implemented as Red-Black Tree. Search, removal, and insertion operations have logarithmic complexity. |
| std::multiset< int, std::greater< int > > | No | Yes | empty, size, swap, count, find | clear, insert, emplase, erase | Contains a sorted set of keys. Multiple keys with same value are allowed. Search, removal, and insertion operations have logarithmic complexity. |
| std::multimap< int, std::string, std::greater< int > > | No | Yes | empty, size, swap, count, find | clear, insert, emplace, erase |  Contains key-value pairs with unique keys. Keys are sorted. Implemented as Red-Black Tree. Search, removal, and insertion operations have logarithmic complexity. |

# Algorithm
```cpp
#include <algorithm>

int main()
{
    std::vector< int > vec{ 4, 5, 6, 7, 8 };

    // for_each
    std::for_each( vec.begin(), vec.end(), []( int x ) { std::cout << x << " "; } );
    std::cout << std::endl;

    std::cout << std::boolalpha;
    std::cout << std::all_of( vec.begin(), vec.end(), []( int x ){ return x > 5; } );
    std::cout << std::endl;

    std::cout << std::any_of( vec.begin(), vec.end(), []( int x ){ return x > 5; } );
    std::cout << std::endl;

    std::cout << std::none_of( vec.begin(), vec.end(), []( int x ){ return x > 10; } );
    std::cout << std::endl;

    std::vector< int >::iterator it = std::find_if( vec.begin(), vec.end(), []( int x ){ return x > 5; } );
    if( vec.end() == it )
    {
        std::cout << "Not found\n";
    }
    else
    {
        std::cout << *it << std::endl;  // 6
    }

    // First element for which the condition is true
    it = std::find( vec.begin(), vec.end(), 7 );
    if( vec.end() == it )
    {
        std::cout << "Not found\n";
    }
    else
    {
        std::cout << *it << std::endl;  // 7
    }

    // First element for which the condition is false
    it = std::find_if_not( vec.begin(), vec.end(), []( int x ){ return x < 5; } );
    if( vec.end() == it )
    {
        std::cout << "Not found\n";
    }
    else
    {
        std::cout << *it << std::endl;  // 5
    }

    vec.push_back( 5 ); // 4, 5, 6, 7, 8, 5
    std::cout << std::count( vec.begin(), vec.end(), 5 ) << std::endl;  // 2
    std::cout << std::count_if( vec.begin(), vec.end(), []( int x ){ return x > 5; } ) << std::endl;    // 3

    // Swap two ranges
    std::vector< int > vec2{ 11, 22, 33, 44 };
    std::swap( vec, vec2 );
    std::for_each( vec.begin(), vec.end(), [](int x){ std::cout << x << ","; } );   // 11,22,33,44,
    std::cout << std::endl;
    std::for_each( vec2.begin(), vec2.end(), [](int x){ std::cout << x << ","; } ); // 4,5,6,7,8,5,
    std::cout << std::endl;

    // Reverse a range of elements
    std::reverse( vec.begin(), vec.begin() + 3 );
    std::for_each( vec.begin(), vec.end(), [](int x){ std::cout << x << ","; } );   // 33,22,11,44,
    std::cout << std::endl;

    // Fill range with given value
    std::fill( vec.begin(), vec.begin() + 3, 100 );
    std::for_each( vec.begin(), vec.end(), [](int x){ std::cout << x << ","; } );   // 100,100,100,44,
    std::cout << std::endl;

    // Apply function to range and put elements at destination
    std::transform( vec.begin(), vec.end(), vec.begin(), []( int x ){ return x + 10; } );
    std::for_each( vec.begin(), vec.end(), [](int x){ std::cout << x << ","; } );   // 110,110,110,54,
    std::cout << std::endl;

    // Copy one range to another
    std::copy( vec2.begin(), vec2.end(), vec.begin() );
    std::for_each( vec.begin(), vec.end(), [](int x){ std::cout << x << ","; } );   // 4,5,6,7, // Size remained same
    std::cout << std::endl;
    // Similarly copy_if

    // Sort a given range
    std::sort( vec.begin(), vec.end(), std::greater< int >() );
    std::for_each( vec.begin(), vec.end(), [](int x){ std::cout << x << ","; } );   // 7,6,5,4,
    std::cout << std::endl;
    // stable_sort - Used to Sort a range while preserving the order of equal elements

    // Replace all elemets which satisfy a given condition
    std::replace_if( vec.begin(), vec.end(), []( int x ){ return x < 7; }, 34 );
    std::for_each( vec.begin(), vec.end(), [](int x){ std::cout << x << ","; } );   // 7,34,34,34,
    std::cout << std::endl;
    // Similarly replace();

    it = std::max_element( vec2.begin(), vec2.end() );
    std::cout << "Max element = " << *it << std::endl;  // Max element = 8
    it = std::min_element( vec2.begin(), vec2.end() );
    std::cout << "Min element = " << *it << std::endl;  // Max element = 4

    std::cout << std::equal( vec.begin(), vec.end(), vec2.begin() ) << std::endl;   // false

    // Returns iterator part the end of valid range
    it = std::remove_if( vec2.begin(), vec2.end(), [](int x){ return x >= 7;  } );
    std::for_each( vec2.begin(), vec2.end(), [](int x){ std::cout << x << ","; } ); // 4,5,6,5,8,5,
    std::cout << std::endl;
    vec2.erase( it, vec2.end() );
    std::for_each( vec2.begin(), vec2.end(), [](int x){ std::cout << x << ","; } ); // 4,5,6,5,
    std::cout << std::endl;
}
```

