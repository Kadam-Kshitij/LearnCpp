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
