## Threads
| Command | Description |
|-----------------|---------------------|
| #include <thread> | Header file |
| std::thread th( func, arg1, arg2, ... ) | Create a new thread and call function func with arguments |
| std::thread th; th = std::thread( func, arg ); | Same as above |
| th.join() | Block the calling thread till thread th completes |
| th.detach() | Detach thread th to run independantly |
| th.joinable() | Check if a thread is already joined or detached |
| std::thread( func, std::ref( a ) ) | Send reference to a thread |
| th.get_id() | Returns the threads id. Return type is std::thread::id |
| std::this_thread::sleep_for( std::chrono::seconds( num ) ); | Sleep current thread for num seconds |
| std::this_thread::get_id(); | Get current thread id |

## Mutex
| Command | Description |
|-----------------|---------------------|
| #include <mutex> | Header file |
| std::mutex mu; | Create a new mutex |
| mu.lock() | Lock mutex. Thread is blovked if mutex is already acquired by another thread. |
| mu.unlock() | Unlock mutex |
| std::lock_guard< std::mutex > lock( mu ); |  Locks the mutex when the std::lock_guard is created and automatically unlocks it when the guard goes out of scope |
| std::unique_lock< std::mutex > lock( mu ); | Provides more flexibility than std::lock_guard, including the ability to unlock and re-lock the mutex manually |
| mu.try_lock() | Attempts to lock the mutex. Returns true if successful, false if the mutex is already locked |
| mu.try_lock_for( time ) | Try to lock mutex for specified time, else return false |
| mu.try_lock_until( time ) | Try to lock mutex until specified time, else return false |


## Condition Variable
| Command | Description |
|-----------------|---------------------|
| #include <condition_variable> | Header file |
| std::condition_variable cv | Create a new condition variable |
| cv.wait( lock, [](){} )  | Condition is checked ones. If true, continues else wait till notified. Again condition is checked when notified. Continues if true, else waiting continues |
| cv.notify_one() | Notify one thread waiting on the cv |
| cv.notify_all() | Notify all thread waiting on the cv |
| cv.wait_for( lock, time ) | Wait for specific time |
| cv.wait_until( lock, time ) | Wait till a particular time |

## Barrier C++20 and above
| Command | Description |
|-----------------|---------------------|
| std::barrier barrier( num ) | Create a new barrier |
| barrier.arrive_and_wait() | A thread arrives at the barrier and waits for others to reach it. Once all threads arrive, they can proceed |

## Future and promise
```cpp
#include <future>

void foo( std::promise< int >& p )
{
    sleep( 2 );
    p.set_value( 23 );    // Set value in promise and unlock any thread waiting on associated future
}

int main()
{
    std::promise< int > p;    // Declare a new promise
    std::future< int > f = p.get_future();    // Retrieve the future associated with the promise

    std::thread th( foo, std::ref( p ) );

    std::cout << "Main thread waiting..." << std::endl;
    int ret = f.get();    // Block till value is set in promise and retrive
    std::cout << ret << std::endl;

    th.join();

    return 0;
}

//Main thread waiting...
//23
```
## Async
```cpp
#include <future>

int foo( int val )
{
    std::cout << "Foo " << std::this_thread::get_id() << ", " << val << std::endl;
    std::this_thread::sleep_for( std::chrono::seconds( 2 ) );
    return val + 3;
}

int main()
{
    std::cout << "Main " << std::this_thread::get_id() << std::endl;
    // foo is executed immediately in a seperate thread
    std::future< int > f = std::async( std::launch::async, foo, 67 );
    std::this_thread::sleep_for( std::chrono::seconds( 2 ) );
    int ret = f.get();
    std::cout << ret << std::endl;
}

//Main 139673786832704
//Foo 139673769543424, 67
//70    // Prints after 2 sec
```
```cpp
#include <future>

int foo( int val )
{
    std::cout << "Foo " << std::this_thread::get_id() << ", " << val << std::endl;
    std::this_thread::sleep_for( std::chrono::seconds( 2 ) );
    return val + 3;
}

int main()
{
    std::cout << "Main " << std::this_thread::get_id() << std::endl;
    // foo is executed in same thread when f.get() is called
    std::future< int > f = std::async( std::launch::deferred, foo, 67 );
    std::this_thread::sleep_for( std::chrono::seconds( 2 ) );
    int ret = f.get();
    std::cout << ret << std::endl;
}

//Main 140540095170368
//Foo 140540095170368, 67   // Prints after 2 secs
//70   // Prints after 4 secs
```
# pthreads
```cpp
pthread_t 
pthread_create( pthread_t*, const pthread_attr_t*, <function>, (void*) );
pthread_join( pthread_t, void** );    // suspend execution of the calling thread until the target thread terminates
pthread_detach( pthread_t );
pthread_exit(void* retval);    // Terminaetes the calling thread.
```
Function handeling the thread - void* foo( void* )
Need to convert pointers to void type before sending and getting.
```cpp
#include <pthread.h>

void* foo( void* ptr )
{
    int* val = ( int* )ptr;
    ++( *val );
    sleep(2);
    std::cout << "val = " << *val << std::endl;

    pthread_exit( ptr );
}

int main()
{
    pthread_t* th = new pthread_t();
    int val{ 88 };
    pthread_create( th, NULL, foo, ( void* )&val );

    int* ret{ nullptr };
    pthread_join( *th, ( void** )&ret );
    printf( "ret = %d\n", *ret );

    pthread_t* th2 = new pthread_t();
    pthread_create( th2, NULL, foo, ( void* )val );
    pthread_detach( *th2 );

    printf( "End" );
}

//val = 89
//ret = 89
//End
```
# Semaphores
```cpp
sem_t
sem_init( sem_t*, int pshared, unsigned int count );
// Here pshread = 0. If not zero then semaphore can be shared between processes.
sem_wait( sem_t* );
sem_post( sem_t* );
sem_destroy( sem_t* );
```
Zero-semaphores -<br>
Here count is initialized to zero. ( sem_init( &s, 0, 0 ); )<br>
Use for strict alteration between two threads.<br>

Binary semaphores -<br>
Count = 1. So it allows only one thread to execute the critical section at a time. So it can be called as a mutex.<br>

Counting semaphores -<br>
Here count > 1. Allows multiple threads to execute within the critical section.<br>

```cpp
sem_t sem;

void* foo( void* ptr )
{
    int* val = ( int* )ptr;

    sem_wait( &sem );
    std::cout << "Thread " << *val << " enter" << std::endl;
    sleep( *val );
    std::cout << "Thread " << *val << " end" << std::endl;
    sem_post( &sem );
}

int main()
{
    pthread_t th[5];
    int* ptr = new int[5];
    sem_init( &sem, 0, 2 );    // Initialize semaphore

    for( int i = 0; i < 5; ++i )
    {
        ptr[i] = i;
        pthread_create( &th[i], NULL, foo, ( void* )&ptr[i] );
    }

    pthread_join( th[0], NULL );
    pthread_join( th[1], NULL );
    pthread_join( th[2], NULL );
    pthread_join( th[3], NULL );
    pthread_join( th[4], NULL );

    delete[] ptr;
    sem_destroy( &sem );   // Destroy semaphore
}

//Thread 4 enter
//Thread 3 enter
//Thread 3 end
//Thread 2 enter
//Thread 4 end
//Thread 0 enter
//Thread 0 end
//Thread 1 enter
//Thread 2 end
//Thread 1 end
```

# Thread Barrier
```cpp

pthread_barrier_t
pthread_barrier_init( pthread_barrier_t*, const pthread_barrierattr_t *attr , unsigned int count );
pthread_barrier_wait( pthread_barrier_t* );
pthread_barrier_destroy( pthread_barrier_t* );
```
Waits till specified number of threads dont reach the barrier point.
Eg - Internet download manager.
```cpp
pthread_barrier_t bar;

void* foo( void* ptr )
{
    int* val = ( int* )ptr;
    std::cout << "Thread " << *val << " Sleep start" << std::endl;
    sleep( *val );
    std::cout << "Thread " << *val << " Sleep done" << std::endl;
    pthread_barrier_wait( &bar );	// Wait till 3 threads reach this point
    std::cout << "Thread " << *val << " return" << std::endl;
}

int main()
{
    pthread_t th[6];
    int* ptr = new int[6];
    pthread_barrier_init( &bar, NULL, 3 );    // Initialize barrier

    for( int i = 0; i < 6; ++i )
    {
        ptr[i] = i;
        pthread_create( &th[i], NULL, foo, ( void* )&ptr[i] );
    }

    pthread_join( th[0], NULL );
    pthread_join( th[1], NULL );
    pthread_join( th[2], NULL );
    pthread_join( th[3], NULL );
    pthread_join( th[4], NULL );

    delete[] ptr;
    pthread_barrier_destroy( &bar );   // Destroy barrier
}

//Thread 2 Sleep start
//Thread 3 Sleep start
//Thread 4 Sleep start
//Thread 5 Sleep start
//Thread 0 Sleep start
//Thread 1 Sleep start
//Thread 0 Sleep done
//Thread 1 Sleep done
//Thread 2 Sleep done
//Thread 2 return
//Thread 1 return
//Thread 0 return
//Thread 3 Sleep done
//Thread 4 Sleep done
//Thread 5 Sleep done
//Thread 5 return
//Thread 4 return
//Thread 3 return
```


============== Thread Termination ==============
pthread_exit( void* retval );


		
# Mutex
```cpp
pthread_mutex_t 
pthread_mutex_init( pthread_mutex_t*, const pthread_mutexattr_t* );
pthread_mutex_lock( pthread_mutex_t* );
pthread_mutex_unlock( pthread_mutex_t* );
pthread_mutex_destroy( pthread_mutex_t* );
```

Code Locking -
In this a part of code is allowed to be executed only by one thread at a time.

Data Locking -
In this the data structure contains a mutex. Only one thread can modify the data at a time.<br>
Two threads may be modifying the same data structure, but if objects are sperate then they can modify the two different objects at the same time.<br>

```cpp
pthread_mutex_t mu;
int global{ 0 };

void* foo( void* )
{
    for( int i = 0; i < 10000; ++i )
    {
        pthread_mutex_lock( &mu );  // Lock Mutex
        global += 1;
        pthread_mutex_unlock( &mu );// Unlock Mutex
    }
}

int main()
{
    pthread_t th[2];
    pthread_mutex_init( &mu, NULL );    // Initialize mutex

    pthread_create( &th[0], NULL, foo, NULL );
    pthread_create( &th[1], NULL, foo, NULL );

    pthread_join( th[0], NULL );
    pthread_join( th[1], NULL );

    std::cout << global << "\n";

    pthread_mutex_destroy( &mu );   // Destroy mutex
}
```

# Condition Variable
```cpp
pthread_cond_t
pthread_init( pthread_cond_t* , pthread_cond_attr_t* );
pthread_cond_wait( pthread_cond_t*, pthread_mutex_t* );
pthread_cond_signal( pthread_cond_t* );
pthread_cond_broadcast( pthread_cond_t* );
pthread_destroy( pthread_cond_t* );
```

When wait signal is triggered, the mutex ownership is changed to be acquired by other thread. The current thread is blocked.<br>
pthread_cond_signal will unlock one thread that are blocked on the specified cv.<br>
pthread_cond_broadcast will unlock all threads that are blocked on the specified cv.<br>

```cpp
pthread_cond_t cv;
pthread_mutex_t mu;
int child_done{ 0 };

void* child( void* )
{
    pthread_mutex_lock( &mu );
    // sleep( 2 );  // 1
    child_done = 1;
    pthread_cond_signal( &cv );
    pthread_mutex_unlock( &mu );
}

void join()
{
    // sleep( 2 );  // 2
    pthread_mutex_lock( &mu );
    while( 1 != child_done )
    {
        std::cout << "Parent waiting" << std::endl;
        // Should be called only after mutex lock
        pthread_cond_wait( &cv, &mu );	// Unlocks the lock and waits. Reloaks mutex when signalled
    }
    pthread_mutex_unlock( &mu );
}

int main()
{
    pthread_cond_init( &cv, NULL );
    pthread_mutex_init( &mu, NULL );

    pthread_t th;
    pthread_create( &th, NULL, child, NULL );

    join();

    std::cout << "End\n";
}

// 1
//Parent waiting
//End

// 2
//End
```
Producer-Consumer problem<br>
```cpp
std::queue< int > que;
pthread_cond_t cv;
pthread_mutex_t mu;
int empty{ 1 };
int global{ 0 };

void* producer( void* )
{
    while( global < 50 )
    {
        pthread_mutex_lock( &mu );
        while( 1 != empty )
            pthread_cond_wait( &cv, &mu );

        while( 3 != que.size() )
        {
            que.push( global );
            ++global;
            sleep( 1 );
        }
        empty = 0;
        pthread_mutex_unlock( &mu );
        pthread_cond_signal( &cv );
    }
}

void* consumer( void* )
{
    while( 1 )
    {
        pthread_mutex_lock( &mu );
        while( 0 != empty )
            pthread_cond_wait( &cv, &mu );

        while( !que.empty() )
        {
            std::cout << "Val - " << que.front() << std::endl;
            que.pop();
            sleep( 1 );
        }
        empty = 1;
        pthread_mutex_unlock( &mu );
        pthread_cond_signal( &cv );

    }
}


int main()
{
    pthread_cond_init( &cv, NULL );
    pthread_mutex_init( &mu, NULL );

    pthread_t thc, thp;
    pthread_create( &thc, NULL, consumer, NULL );
    pthread_create( &thp, NULL, producer, NULL );

    pthread_join( thc, NULL );
    pthread_join( thp, NULL );

    std::cout << "End\n";
}
```
