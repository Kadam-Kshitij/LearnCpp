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

| Type | Push/Pop |
|------|----------|
| std::array< int , 6 >| No |
| std::vector< int > | push_back, pop |
| std::deque< int > | push_back, push_front, pop_back, pop_front |
| std::list | |
| std::queue< int > | push, pop |
| std::stack< int > | push, pop |
| std::priority_queue< int, std::vector< int >, std::greater< int > > | push, pop |
| std::set< int, std::greater< int > > | |

